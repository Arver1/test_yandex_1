# Задание «Комплексное» («Найди ошибки»)

## Описание поиска и перечня ошибок.

> Использую редактор WebStorm и Chrome Web Browser.

1.Отсутствие export default или {initMap}
>Обратил внимание при сборке,что не найден initMap при импорте.

2.Не заданы размеры контейнера \#map.
>Запустил приложение,браузер ничего не отобразил.Открыл инструменты разработчика.Посмотрел,что в консоли ошибок нет.Далее решил посмотреть контейнер \#map,увидел,что в блочной модели размеры 956x0.
Так как уже работал с API Яндекс.Карт на курсах HTML Academy добавил размеры в css.
```
html,
body,
#map {
  margin: 0;
  padding: 0;
  ***width: 100%***;
  ***height: 100%***;
}
```
3.Отсутствие myMap.geoObjects.add(objectManager).
>После изменений перезапустил webpack,так как из коробки изменения из css файла не подтянутся.
Далее начал разбираться построчно в функции initMap до комментариев details и filters,предположив,что детали,а уж тем более фильтры не влияют на то,что метки не отображаются и вероятно ошибка в loadList.Через console.log() посмотрел,что данные(коллекция сущностей) соотвествуют описанию в справочнике v.2.1.68.Посмотрел там же пример,а добавления геообъектов на карту нет,случайность?-Нет не думаю.-).


4.Поменял местами параметры lat и long.
>Был крайне удивлен,что карта вновь пустая.Решил посмотреть пример в песочнице(раздел метка) и меня смутил момент,что координаты были [55.8, 37.8],а до этого в консоли первый элемент был с полностью противоположными координатами и это все на одной карте Москвы и явно не просто совпадение...

5.Если неисправный объект входит в кластер, то иконка кластера должна показывать, что в нем есть неисправная станция.
>Так как был задан только один цвет 
```
objectManager.clusters.options.set('preset', 'islands#greenClusterIcons');>
```
>Дописал:
```
function setColorNonWorkCluster(){
    const clusters = objectManager.clusters.getAll();
    if(clusters) {
      clusters.forEach(cluster => {
        if(cluster.properties.geoObjects.some(it => !it.isActive)) {
          objectManager.clusters.setClusterOptions(cluster.id, {
            preset: 'islands#redClusterIcons'
          });
        }
      });
    }
  }

  loadList().then(data => {
    objectManager.add(data);
    setColorNonWorkCluster();
  });

  myMap.events.add('boundschange', function () {
    setColorNonWorkCluster();
  });>
```
6.Потеря контекста.
>При клике popup сыпались ошибки,ну как же иначе?))
Макет пользовательский, и я решил убедиться,что проблема действительно в нем,закомментировал строку 
```
//geoObjectBalloonContentLayout: getDetailsContentLayout(ymaps)
```
>и дописал obj.properties.balloonContent = 'Hello';
```if (!obj.properties.details) {
      loadDetails(objectId).then(data => {
        obj.properties.details = data;
        obj.properties.balloonContent = 'Hello';
        objectManager.objects.balloon.setData(obj);
      });
    }
```
>При открытии popup ошибки не сыпались,значит дело действительно в макете.Так как функции были стрелочные,через console.log() посмотрел,чему равно this.

7.Неправильное значение max=0.
>Эту ошибку нашел практически сразу,так как при просмотре сразу бросилось в глаза 
```
yAxes: [{ ticks: { beginAtZero: true, max: 0 } }]
```
>Заменил max на min и все заработало.)
