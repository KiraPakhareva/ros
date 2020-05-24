# Среда Gym

Как я уже сказал, среда определяет доступные агенту действия, как вычислить вознаграждение на основе его действий и результатов, и как получить состояние мира агента, после того, как эти действия были выполнены. OpenAI предоставляет стандартизированный способ создания окружающей среды. В принципе, вы должны создать класс среды, который должен наследовать от gym.Env. Это наследование дает вам право реализовать в этом классе функции \_seed, \_reset и \_step \(объясненные выше\). В нашем случае мы создали класс под названием QuadCopterEnv . Вы можете найти код в файле myquadcopter\_env.py.

Код начинается с регистрации класса в пуле доступных сред OpenAI. Вы регистрируете новую среду со следующим кодом:



```text
reg = register(
 id='QuadcopterLiveShow-v0',
 entry_point='myquadcopter_env:QuadCopterEnv',
 timestep_limit=100,
 )
```

Затем класс начинает инициализацию разделов, к которым он должен подключиться, получает параметры конфигурации от сервера Ros param и подключается к Gazebo.

```text
def __init__(self):
 
   self.vel_pub = rospy.Publisher('/cmd_vel', Twist, queue_size=5)
   self.takeoff_pub = rospy.Publisher('/drone/takeoff', EmptyTopicMsg, queue_size=)
 
   self.speed_value = rospy.get_param("/speed_value")
   self.desired_pose = Pose()
   self.desired_pose.position.z = rospy.get_param("/desired_pose/z")
   self.desired_pose.position.x = rospy.get_param("/desired_pose/x")
   self.desired_pose.position.y = rospy.get_param("/desired_pose/y")
   self.running_step = rospy.get_param("/running_step")
   self.max_incl = rospy.get_param("/max_incl")
   self.max_altitude = rospy.get_param("/max_altitude")
 
   self.gazebo = GazeboConnection()
 
   self.action_space = spaces.Discrete(5) #Forward,Left,Right,Up,Down
   self.reward_range = (-np.inf, np.inf)
 
   self._seed()
```

Теперь настало время для определения каждой из обязательных функций для окружающей среды.   
• В функции \_seed мы инициализируем случайное поле, необходимое для генерации случайных чисел. Оно используются алгоритмом обучения при создании случайных действий   
• В функции \_reset мы инициализируем всю среду до известного начального состояния, поэтому все эпизоды могут начинаться с одних и тех же условий. Очень простой код. Просто сбрасывает моделирование и очищает темы. Единственная особенность заключается в том, что в конце функции мы приостанавливаем моделирование, потому что мы не хотим, чтобы робот работал, пока мы выполняем другие вычислительные задачи. В противном случае мы не смогли бы гарантировать начальные условия для всех эпизодов, так как это во многом зависело бы от времени выполнения других алгоритмов в обучающем компьютере.   
• Основная функция-это функция \_step. Это то, что вызывается во время циклов обучения. Эта функция получает в качестве параметра действие, выбранное алгоритмом обучения. Помните, что этот параметр является только номером выбранного действия, а не фактическим действием. Алгоритм обучения не знает, какие действия мы имеем для этой задачи. Он просто знает количество доступных действий и выбирает одно из них, основываясь на своем текущем состоянии обучения. Таким образом, мы получаем здесь только номер действия, выбранного алгоритмом обучения.

Первое, что мы делаем, - это преобразуем это число в фактическую команду действия для робота. Вот что делает этот код, он преобразует число в действие движения, которое мы отправим роботу ROS:

```text
 vel_cmd = Twist()
 if action == : #FORWARD
 vel_cmd.linear.x = self.speed_value
 vel_cmd.angular.z = 0.0
 elif action == 1: #LEFT
 vel_cmd.linear.x = 0.05
 vel_cmd.angular.z = self.speed_value
 elif action == 2: #RIGHT
 vel_cmd.linear.x = 0.05
 vel_cmd.angular.z = -self.speed_value
 elif action == 3: #Up
 vel_cmd.linear.z = self.speed_value
 vel_cmd.angular.z = 0.0
 elif action == 4: #Down
 vel_cmd.linear.z = -self.speed_value
 vel_cmd.angular.z = 0.0
```

Следующим шагом является отправка действия роботу. Для этого нам нужно разорвать связь с симулятором, отправить команду, подождать некоторое время для выполнения команды, сделать наблюдение за состоянием окружающей среды после выполнения и снова приостановить работу симулятора.

```text
 self.gazebo.unpauseSim()
 self.vel_pub.publish(vel_cmd)
 time.sleep(self.running_step)
 data_pose, data_imu = self.take_observation()
 self.gazebo.pauseSim()
```

Затем мы обрабатываем текущее состояние робота / окружающей среды для расчета вознаграждения. Для получения вознаграждения мы учитываем, насколько близко к нужному положению находится беспилотник, но и другие факторы, такие как наклон дрона или его высота. Кроме того, мы выступаем за движение вперед против поворота.

```text
reward,done = self.process_data(data_pose, data_imu)
 if action == :
   reward += 100
 elif action == 1 or action == 2:
   reward -= 50
 elif action == 3:
   reward -= 150
 else:
   reward -= 50
```

Наконец, мы возвращаем текущее состояние, полученную награду и флаг, указывающий, должен ли этот эпизод считаться выполненным \(либо потому, что дрон достиг цели, либо потому, что он пошел против условий высоты или наклона\).

```text
 state = [data_pose.position.x]
 return state, reward, done, {}
```

![](https://www.theconstructsim.com/wp-content/uploads/2018/02/drunken_drone2-2018-02-09_08_53_44.gif)

> Можешь также прочитать: [https://www.theconstructsim.com/why-i-choose-ros-for-my-master-thesis/](https://www.theconstructsim.com/why-i-choose-ros-for-my-master-thesis/)

В общем, так оно и есть. Приведенный выше код вызывает дополнительные функции, которые можно проверить, заглянув в класс QuadCopterEnv. Это функции, которые выполняют грязную работу по вычислению фактических значений, но нам не нужно рассматривать их здесь, потому что они выходят за рамки этого предмета.

Однако одна из функций, которую нам, возможно, придется охватить, - это функция, которая вычисляет вознаграждение. Ее код является следующим:

```text
def process_data(self, data_position, data_imu):
 done = False
 euler = tf.transformations.euler_from_quaternion([data_imu.orientation.x,
                                                   data_imu.orientation.y,
                                                   data_imu.orientation.z,
                                                   data_imu.orientation.w])
 roll = euler[]
 pitch = euler[1]
 yaw = euler[2]
 
 pitch_bad = not(-self.max_incl &lt; pitch &lt; self.max_incl)
 roll_bad = not(-self.max_incl &lt; roll &lt; self.max_incl)
 altitude_bad = data_position.position.z &gt; self.max_altitude
 
 if altitude_bad or pitch_bad or roll_bad:
   rospy.loginfo ("(Drone flight status is wrong) &gt;&gt;&gt; ("+str(altitude_bad)+","+str(pitch_bad)+","+str(roll_bad)+")")
   done = True
   reward = -200
 else:
   reward = self.improved_distance_reward(data_position)
 
 return reward,done
```

Этот код, в основном, делает две вещи:   
1. Во-первых, обнаруживает, если робот превысил ранее определенные рабочие пределы высоты и наклона. Если это так, он считает, что эпизод завершен   
2. Вычисляет вознаграждение на основе расстояния до цели



