# AngularMonitoring

This is simple AngularJS + Bootstrap code, which includes all requirements from google and bootstrap remote servers. It also uses 3rd party query API service http://api.minetools.eu/. Advanced users can use all external data from their own servers, but this release designed for one copy-paste installation.

![Alt text](http://i.imgur.com/1QDJJEC.png)

## Features: 
- extremely simple to install
- AJAX
- easy to castomize
- bootstrap and responsible design
- you can add icon for dynmap and badge for game version in config

## Installation
Place this code in ```<head>``` of your page:
```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
<!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
<!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
<!--[if lt IE 9]>
<script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
<script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
<![endif]-->
```

And this code in place you want monitoring to be (All ```<script>``` data can be placed at the bottom of page):

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
	 // Settings
	
	 // AJAX request frequency seconds (don't use faster then 10 seconds for external query api)
	 var interval = 10;
	 // Server list
	
	 $scope.servers = {
	 // Just list all your servers like this
	     // Server name
	     'Owlcraft': {
	         // Server adress
	         'IP':   'owlcraft.ru',
	         // Server port
	         'port': '25565',
	         // Server map url (not necessarily to add)
	         'map':  'http://classic.map.owlcraft.ru/',
	         // Server version badge (not necessarily to add)
	         'badge': '1.9'
	     },
	     'Owltech': {
	         'IP':   '212.3.145.109',
	         'port': '25566'
	     }
	 };
	 // End settings
	
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
	         $scope.servers[server].label = $sce.trustAsHtml('<span class="label label-warning" title="Monitoring service not available">N/A</span>'); 
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

Now just add your servers in list here:
```javascript
$scope.servers = {
// Just list all your servers like this
// Server name
	'Owlcraft': {
		// Server adress
		'IP':   'owlcraft.ru',
		// Server port
		'port': '25565',
		// Server map url (not necessarily to add)
		'map':  'http://classic.map.owlcraft.ru/',
		// Server version badge (not necessarily to add)
		'badge': '1.9'
	},
	'Owltech': {
		'IP':   '212.3.145.109',
		'port': '25566'
	}
};
```
