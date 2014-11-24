---
layout: post
title: "Apache Cordova tutorial part4"
categories: Cordova
---

前面两篇[apache-cordova-tutorial-part1](/20141116/apache-cordova-tutorial-part1/) 和 [apache-cordova-tutorial-part2](/20141120/apache-cordova-tutorial-part2/) 和 [apache-cordova-tutorial-part3](/20141123/apache-cordova-tutorial-part3/)已经将项目初步运行起来了。

接下来，调用一下Location, Contacts, Camera等API，在真机上安装App进行测试。

安装并使用geolocaton插件

	cordova plugin add org.apache.cordova.geolocation
修改index.html，添加以下list item标签内容到employee-tpl模板中：

	<li class="table-view-cell media">
	  <a hre="#" class="push-right add-location-btn">
	      <span class="media-object pull-left"></span>
	      <div class="media-body">
	          Add location
	      </div>
	  </a>
	</li>
修改`js/EmployeeView.js`文件，在EmployeeView的initialize()函数中，注册`.add-location-btn`的click事件：

	this.$el.on('click', '.add-location-btn', this.addLocation);

	this.addLocation = function(event) {
	  event.preventDefault();
	  navigator.geolocation.getCurrentPosition(
	      function(position) {
	          alert(position.coords.latitude + ',' + position.coords.longitude);
	      },
	      function() {
	          alert('Error getting location');
	      });
	  return false;
	};

重新编译，并在真机上安装App进行测试。

安装并使用contacts插件

	cordova plugin add org.apache.cordova.contacts
修改index.html，添加以下list item标签内容到employee-tpl模板中：

	<li class="table-view-cell media">
	    <a hre="#" class="push-right add-contact-btn">
	        <span class="media-object pull-left"></span>
	        <div class="media-body">
	            Add to contacts
	        </div>
	    </a>
	</li>
修改`js/EmployeeView.js`文件，在EmployeeView的initialize()函数中，注册`.add-contact-btn`的click事件：

	this.$el.on('click', '.add-contact-btn', this.addToContacts);

	this.addToContacts = function(event) {
	    event.preventDefault();
	    console.log('addToContacts');
	    if (!navigator.contacts) {
	        alert("Contacts API not supported", "Error");
	        return;
	    }
	    var contact = navigator.contacts.create();
	    contact.name = {givenName: employee.firstName, familyName: employee.lastName};
	    var phoneNumbers = [];
	    phoneNumbers[0] = new ContactField('work', employee.officePhone, false);
	    phoneNumbers[1] = new ContactField('mobile', employee.cellPhone, true);
	    contact.phoneNumbers = phoneNumbers;
	    contact.save();
	    return false;
	};
重新编译，并在真机上安装App进行测试。

安装并使用camera插件

	cordova plugin add org.apache.cordova.camera
修改index.html，添加以下list item标签内容到employee-tpl模板中：

	<li class="table-view-cell media">
	  <a hre="#" class="push-right change-pic-btn">
	      <span class="media-object pull-left"></span>
	      <div class="media-body">
	          Change Picture
	      </div>
	  </a>
	</li>
修改`js/EmployeeView.js`文件，在EmployeeView的initialize()函数中，注册`.change-pic-btn`的click事件：

	this.$el.on('click', '.change-pic-btn', this.changePicture);

	this.changePicture = function(event) {
	  event.preventDefault();
	  if (!navigator.camera) {
	      alert("Camera API not supported", "Error");
	      return;
	  }
	  var options =   {   quality: 50,
	                      destinationType: Camera.DestinationType.DATA_URL,
	                      sourceType: 1,      // 0:Photo Library, 1=Camera, 2=Saved Album
	                      encodingType: 0     // 0=JPG 1=PNG
	                  };

	  navigator.camera.getPicture(
	      function(imgData) {
	          $('.media-object', this.$el).attr('src', "data:image/jpeg;base64,"+imgData);
	      },
	      function() {
	          alert('Error taking picture', 'Error');
	      },
	      options);

	  return false;
	};
重新编译，并在真机上安装App进行测试。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
