# Использование OpenAI совместно с ROS

OpenAI выпустила Gym, инструментарий для разработки и сравнения алгоритмов обучения армированию \(RL\). Этот инструментарий является огромной возможностью для ускорения прогресса в создании лучших алгоритмов подкрепления, поскольку он обеспечивает простой способ их сравнения, на одних и тех же условиях, независимо от того, где выполняется алгоритм. Инструментарий, в основном, направлен на создание RL алгоритмов для общего абстрактного агента. Здесь мы заинтересованы в применении его к управлению роботами \(конечно!\). В частности, нас интересуют роботы на базе ROS. Именно поэтому, в этом посте мы опишем как применить Gym OpenAI для управления дроном, который работает с ROS Давайте рассмотрим пример обучения.

