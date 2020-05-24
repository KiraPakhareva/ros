# Как настроить тест

Вы можете найти файл yaml в каталоге config, содержащий различные параметры, необходимые для настройки задачи обучения. Я разделил параметры на два типа:   
1. Параметры, связанные с используемым алгоритмом обучения: это те параметры, которые нужны моему алгоритму. В этом случае они предназначены специально для алгоритма Qlearn. Вы бы определили здесь те, которые нужны вашему алгоритму, а затем прочитали их в файле start\_training.py.   
2. Параметры, связанные с окружающей средой: это параметры, которые влияют на способ получения вознаграждения, и, следовательно, они влияют на окружающую среду. К ним относятся положение цели или условия, при которых эпизод может считаться прерванным из-за нестабильных условий беспилотника \(слишком большая высота или слишком большой наклон\).
