## тестовый deployment


### P.S.

Для условий 
```
- по результатам нагрузочного теста известно, что 4 пода справляются с пиковой нагрузкой
- на первые запросы приложению требуется значительно больше ресурсов CPU, в дальнейшем потребление ровное в районе 0.1 CPU. По памяти всегда “ровно” в районе 128M memory
- приложение имеет дневной цикл по нагрузке – ночью запросов на порядки меньше, пик – днём
```
Чтобы минимизировать потребление ресурсов при отказоустойчивости, можно воспользовать механизмами VPA (vertical pod autoscaling) и HPA (horizontal pod autoscaling), логика будет такая:
1. запустить приложение в 4-х репликах (условие максимальной отказоустойчивости);
2. настроить VPA таким образом, чтобы поды стартовали с 1 CPU, а в дальнейшем, vpa бы в зависимости от потребления выставлял 0.1 CPU;
3. настроить HPA, чтобы он контролировал кол-во реплик, но если HPA и VPA будут отлеживать по одним и тем же метрикам, то они будут работать некорректно, поэтому я считаю что нужно настроить HPA на кастомные метрики по кол-ву запросов, чтобы ночью кол-во реплик сокращалось тем самым сэкономив ресурсы.

