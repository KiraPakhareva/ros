# Пример обучения дронов

В этом примере мы будем обучать дрон на основе ROS, чтобы иметь возможность передвижения в пространстве как можно ниже \(может быть, чтобы избежать обнаружения\), но избегая препятствий на своем пути. Для разработки алгоритма мы будем использовать Ros Development Studio \(RDS\). Это среда, которая позволяет программировать с ROS и его моделированием с помощью веб-браузера, без необходимости устанавливать что-либо на компьютере. Таким образом, у нас есть все необходимые пакеты для ROS и OpenAI Gym и GAZEBO уже установлены. Вы можете следить за остальной частью поста двумя способами:



1. Либо зайдите в Ros Development Studio \([https://auth.theconstructsim.com/auth/login/?next=/oidc/authorize/%3Fscope%3Dopenid%2Bprofile%2Bemail%26state%3DK4lfGqrlmu0WENeO1dUm2awpsqdpdYHQ%26redirect\_uri%3Dhttps%253A%252F%252Frds.theconstructsim.com%252Foidc%252Fcallback%252F%26response\_type%3Dcode%26client\_id%3D983516](https://auth.theconstructsim.com/auth/login/?next=/oidc/authorize/%3Fscope%3Dopenid%2Bprofile%2Bemail%26state%3DK4lfGqrlmu0WENeO1dUm2awpsqdpdYHQ%26redirect_uri%3Dhttps%253A%252F%252Frds.theconstructsim.com%252Foidc%252Fcallback%252F%26response_type%3Dcode%26client_id%3D983516)\) и создайте бесплатную учетную запись.
2. Либо установите все на своем компьютере. Если вам нужно установить эти пакеты, посмотрите здесь \([http://wiki.ros.org/ROS/Installation](http://wiki.ros.org/ROS/Installation)\) для установки ROS, здесь \([https://gym.openai.com/docs/](https://gym.openai.com/docs/)\) для установки OpenAI и здесь \([https://bitbucket.org/theconstructcore/workspace/projects/PS](https://bitbucket.org/theconstructcore/workspace/projects/PS)\) для загрузки моделирования Gazebo \(для Indigo + Gazebo 7\).

Ниже приведены инструкции по разработке программы обучения с помощью RDS, но, как вы можете видеть, эти шаги одинаковы для вашей локальной установки.



