# AngularMonitoring

Это простой одностраничный AngularJS + Bootstrap minecraft мониторинг, в который подключены все зависимости с удаленных серверов. Также используется сторонний API сервис для мониторинга http://api.minetools.eu/,
работоспособность которого не гарантируется. Продвинутые пользователи могут загрузить все зависимости на собственный сервер, включая получение информации о серверах,
однако данный релиз предназначен для наиболее простой и быстрой установки в два клика.

![Alt text](http://i.imgur.com/1QDJJEC.png)

## Возможности: 
- очень легко устанавливается
- AJAX
- легко кастомизируется
- bootstrap и адаптивный дизайн
- возможность добавить иконку со ссылкой на dynmap и бэйдж с версией сервера

## Установка
Добавьте этот код в ```<head>``` вашей html страницы:
```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
```

Добавьте этот код в то место, где хотите разместить мониторинг (Все ```<script>``` теги можно разместить в самом низу страницы):

```html
<ul class="list-unstyled" ng-app="AngularMonitoring" ng-controller="MonitoringController" ng-cloak>
	<li class="list-group-item" ng-repeat="(server, info) in servers">
		<h5>
			<a ng-if="info.map" class="glyphicon glyphicon-globe" target="_blank" ng-href="{{info.map}}"></a> 
			<strong ng-bind="server"></strong> 
			<span ng-if="info.badge" class="label label-success" ng-bind="info.badge"></span><span class="pull-right" ng-bind-html="info.label"></span>
		</h5>
		<div class="progress">
			<div class="progress-bar" role="progressbar" ng-class="info.class"
				aria-valuemin="0" aria-valuemax="100" ng-style="{'width': (info.width + '%')}">
			</div>
		</div>
	</li>
</ul>
<!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"></script>
<!-- Latest compiled and minified JavaScript -->
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
<!-- AngularJS -->
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.29/angular.min.js"></script>
<script type="text/javascript">
	angular.module('AngularMonitoring', [])
	.controller('MonitoringController', function($scope, $http, $interval, $sce){
	 // Настройки
	
	 // Частота обновления мониторинга в секундах(не ставьте быстрее чем 10 секунд для стороннего api)
	 var interval = 10;
	 // Список серверов
	
	 $scope.servers = {
	 // Просто перечислите все ваши сервера
	     // Имя
	     'Owlcraft': {
	         // Адрес сервера
	         'IP':   'owlcraft.ru',
	         // Порт сервера
	         'port': '25565',
	         // Ссылка на вебкарту сервера (можно не добавлять)
	         'map':  'http://classic.map.owlcraft.ru/',
	         // Бэйдж с версией сервера (можно не добавлять)
	         'badge': '1.9'
	     },
	     'Owltech': {
	         'IP':   '212.3.145.109',
	         'port': '25566'
	     }
	 };
	 // Конец настроек
	
	 var getWidth = function(server) {
	     return Math.round((($scope.servers[server].data.players.online * 1.0) / $scope.servers[server].data.players.max) * 100);
	 };
	
	 var getLabel = function(server) {
	     return $sce.trustAsHtml($scope.servers[server].data.players.online + ' / ' + $scope.servers[server].data.players.max);
	 };
	
	 var getClass = function(server) {
	     if ($scope.servers[server].width < 50) 
	         return 'progress-bar-success';
	     else if ($scope.servers[server].width < 80)
	         return 'progress-bar-warning';
	     else
	         return 'progress-bar-danger';
	 };
	
	 $scope.query = function(server, info){
	
	     $http.get('http://api.minetools.eu/ping/' + info.IP + '/' + info.port, {timeout: interval * 1000})
	     .then(function(response){
	         $scope.servers[server].data = response.data;
	         if (typeof response.data.error === 'undefined') {
	             $scope.servers[server].label = getLabel(server); 
	             $scope.servers[server].width = getWidth(server);
	             $scope.servers[server].class = getClass(server);
	         } else {
	             $scope.servers[server].label = $sce.trustAsHtml('<span class="label label-danger">OFFLINE</span>'); 
	             $scope.servers[server].width = 100;
	             $scope.servers[server].class = 'progress-bar-danger progress-bar-striped active';
	         }
	     },
	     function(){
	         $scope.servers[server].data = {};
	         $scope.servers[server].label = $sce.trustAsHtml('<span class="label label-warning" title="Сервис мониторинга не доступен">N/A</span>'); 
	         $scope.servers[server].width = 100;
	         $scope.servers[server].class = 'progress-bar-warning progress-bar-striped active';
	     });
	 };
	
	 $scope.queryAll = function() {
	     for (var server in $scope.servers) {
	         $scope.query(server, $scope.servers[server]);
	     }
	 };
	
	 $scope.queryAll();
	 $interval($scope.queryAll, interval * 1000);
	
	});
</script>
```

Теперь просто настройте ваши сервера:
```javascript
$scope.servers = {
// Просто перечислите все ваши сервера
// Имя сервера
	'Owlcraft': {
		// Адрес сервера
		'IP':   'owlcraft.ru',
		// Порт сервера
		'port': '25565',
		 // Ссылка на вебкарту сервера (можно не добавлять)
	   'map':  'http://classic.map.owlcraft.ru/',
	   // Бэйдж с версией сервера (можно не добавлять)
	   'badge': '1.9'
	},
	'Owltech': {
		'IP':   '212.3.145.109',
		'port': '25566'
	}
};
```

## В планах:
- добавить php скрипт для выдачи информации о серверах
- собрать отдельный css для предотвращения конфликтов
