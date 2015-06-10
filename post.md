**Disclaimer :** Before we dive in, I'd like you to know this is **not the true** social networking app out there, but it has quite a ton of features which these kind of apps usually have and this post basically demonstrates how to build **data-storage, search and real-time** in the apps which you build.

The social networking app which we will build today will look like this :

![A sample social networking app built by cloudboost.io](https://blog.cloudboost.io/content/images/2015/05/Capture1.PNG)

If you want to check the demo out, We have hosted the project here : [Demo : http://cloudboost.github.io/socialNetApp/](http://cloudboost.github.io/socialNetApp/)

If you're looking for GitHub URL to check the code, You can find the project here : [https://github.com/CloudBoost/CloudBoost.github.io/tree/master/socialNetApp](https://github.com/CloudBoost/CloudBoost.github.io/tree/master/socialNetApp)

##Before you begin :

* You need to signup with a free account on CloudBoost [here](https://www.cloudboost.io).
* You need to create a new app. Type in the name and the AppID and click create. ==Hint : AppID is just the unique name of your app on the CloudBoost Network.==

![Create a new CloudBoost App](https://blog.cloudboost.io/content/images/2015/05/createApp.PNG)

![CloudBoost app which is created](https://blog.cloudboost.io/content/images/2015/05/AppCreated.PNG)

* After you create your app, Click on "Keys" to check out your Client Key. 

==Hint: **Client Key** is used if you're using the SDK on the client and not the server. If you're using the SDK like our NodeJS SDK on the server. Pleas use **Master Key** instead  ==

![Your CloudBoost.io app keys.](https://blog.cloudboost.io/content/images/2015/05/AppKeys.PNG)

##Step 1 : Build the UI of your Social Networking App.

We've used Bootstrap to build the UI. If you haven’t used bootstrap before. You can learn bootstrap from their documentation [here](http://getbootstrap.com/getting-started/). We recommend know Bootstrap before you proceed, if you don't or are not interested in the HTML fluff, you can always download our UI from [here](https://github.com/CloudBoost/sample-social-network/blob/master/sample.html) and proceed to Step 2. 

* **Link Bootstrap Stylesheets in the head of your HTML page.** 

		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css">
		<title>Sample Social Network build with CloudBoost</title>

    
    
    
    
* **Create the header of the page with the search form.**

![Social Networking app header](https://blog.cloudboost.io/content/images/2015/05/header.PNG)
    
    <form class="navbar-form navbar-left form-inline" role="search" id="searchForm">
					<div class="form-group">
						<div class="input-group" style="display:table;">
							<input type="text" class="form-control" id="search" placeholder="Search Anything">
							<span class="input-group-btn">
								<button class="btn btn-warning bt-sm" type="submit"><span class="glyphicon glyphicon-search"></span></button>
							</span>
						</div>
					</div>
				</form>
                

**For Notifications :**

					<li><a href="./sample.html">Feeds</a></li>
					<li class="dropdown">
						 <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-expanded="false"><span style="font-size:25px;" class="glyphicon glyphicon-globe"><span class="badge" id="count" style="font-size:10px"></span></span></a>
						<ul class="dropdown-menu" id="noticeBar" role="menu">
							<li><a href="#">You Have No Notification</a></li>
						</ul>
					</li>



* **Adding a "Post" form** :
![Create a post form](https://blog.cloudboost.io/content/images/2015/05/postInsert.PNG)


				<form class="form-inline" id="postForm">
					<div class="form-group">
						<input type="text" class="form-control" name="name" id="name" value="" placeholder="Your Name"></input>
					</div>
					<div class="form-group" >
						<input type="text" class="form-control" name="text" id="text" value="" placeholder="Post Anything"></input>
					</div>
					<button class="btn btn-primary" type="submit">Post</button>
				</form>

* **Add a list item** : 
![Adding a list item](https://blog.cloudboost.io/content/images/2015/05/listItem.PNG)


Create an empty `<div>` with an ID `feed`. We dont add any content to this div because it'll be populated when things will load from the server. 

You should now have built the UI of the app, Proceed to Step 2 where we add the flesh and make it all work! 

If you want to look at the sample HTML code of the UI. Please check out our Git Repo [here](https://github.com/CloudBoost/sample-social-network)

##Step 2 : Build a feature to save "Posts".

Here we will implement a feature to save a post in CloudBoost database. 

* Create a table in the Table Designer at CloudBoost.io
![Go to table Designer](https://blog.cloudboost.io/content/images/2015/05/Data.PNG)

![Add new table](https://blog.cloudboost.io/content/images/2015/05/addNEwTbale.PNG)

* Once your new table is created, Add two fields to it : **name** of type **text** and **post** of type **text**. It should look like the screen below : 

![Your feed table schema](https://blog.cloudboost.io/content/images/2015/05/tableSchema.PNG)

* Once a new table is created, You can start hacking with our JavaScript SDK. Download our JavaScript SDK from [here](https://docs.cloudboost.io)

You need to link the SDK in the `<head>` section of your HTML page. 

`<script src="https://cloudboost.io/js-sdk/1.0.0.js"></script>`

* Once you've linked the SDK. You need to Initialize your new CloudBoost App. Create a new file called *app.js* and You can add this code inside of that file which will initialize your new CloudBoost App. 

`var appId="YOUR APP ID"; //This should be your AppID`

`var appKey="YOUR CLIENT KEY"; //this should be your AppKey`

Once this is done, You can now call CB.CloudApp.init() function which will init your app. 


    CB.CloudApp.init(appId,appKey,{
	    success: function(){
	    	console.log("App is connected to cloudboost and ready to use");
    	},
    	error: function(){
    		console.log("Oops! somthing went wrong. better check your appId and key");
    	}
    });


Your app is now ready. Next step is to save the new "Post"

* **Saving the post :** 
* Create a new function which will help you save a post. You basically need to attach that function to the submit event of the post button which we have just created.

        $("#postForm").submit(function() {
        	var obj = new CB.CloudObject("feed");	
        	obj.isSearchable = true;
        	obj.set("name", $("#name").val());
        	obj.set("post",$("#text").val());
        	//saving post
        	obj.save({ 
        		success: function(obj) { 
        			console.log('posted to sample app');
        		},
        		error: function(err) { 
        			console.log("oops!! a problem occured while posting.. please try again.");
        		}
        	});
        	return false;
        });

* **Query all the posts when page loads :** 
* You need to query all the post when the page loads, or when a new post is added. You basically need to create a function to query the post table and get all the posts. 

        function queryFeeds(){
        		//creating an object for querying 'feed' data
        		var query = new CB.CloudQuery("feed"); 
        		query.orderByDesc('createdAt');
        		query.find({ 
        			//query will return a list of CloudObjects.
        			success: function(list){ 
        				//extracting and appending data into html page from list of CloudObjects
        				for (i = 0; i < list.length; i++) { 
        					$("#feed").append("<div class='panel panel-default'><div class='panel-body' style=''><div class='col-md-1 col-sm-2 col-xs-3'><img src='image/profile.png' class='img-responsive img-circle'/></div><div><strong>"+list[i].get('name')+"</strong><br/>"+list[i].get('post')+"</div></div></div>");
        				}
        			},
        			error: function(err){ 
        				console.log("unable to fetch data");
        			} 
        		});
        }


You need to call this function in the init event of the CloudApp, so when the CloudApp is successfully initialized, all the posts are queried and displayed on the UI. 

        CB.CloudApp.init(appId,appKey,{
        	success: function(){
        		console.log("App is connected to cloudboost and ready to use");
        		queryFeeds(); //this function is now added to App Init event. 
        	},
        	error: function(){
        		console.log("Oops! somthing went wrong. better check your appId and key");
        	}
        });


##Step 3 : Implement Real-time notifications.

Real-time notifications are web-socket notifications that fires a function whenever something changes in the database. We'll add a notification whenever someone inserts a new posts in the table. 

* **Attach a handler** : 

This function inside of CLoudObject.on will fire whenever someone inserts a post into the database. 

        function attachRealTimeNotificationHandler(){
        
        	CB.CloudObject.on('feed','created',function(cloudObj){
        			//getting realtime feeds
        			$("#feed").prepend("<div class='panel panel-default'><div class='panel-body'><div class='col-md-1 col-sm-2 col-xs-3'><img src='image/profile.png' class='img-responsive img-circle'/></div><div><strong>"+cloudObj.get('name')+"</strong><br/>"+cloudObj.get('post')+"</div></div></div>");
        			noticeCount = noticeCount + 1;
        			$("#count").text(noticeCount);
        			$("#noticeBar").empty().append("Your friend shared a post");
        		},
        		{
        			success : function(){ 
        				console.log("CloudObject.on executed");
        				
        			},
        			error : function(err){
        				console.log("error in CloudObect.on");
        			} 
        		}); 
        
        }

* **Call the function in the App Init event** : 

        CB.CloudApp.init(appId,appKey,{
        	success: function(){
        		console.log("App is connected to cloudboost and ready to use");
        		//CloudBoost RealTime
        		attachRealTimeNotificationHandler();
        		queryFeeds();
        	},
        	error: function(){
        		console.log("Oops! somthing went wrong. better check your appId and key");
        	}
        });


##Step 4: Implement Search with CloudSearch. 

You need to attach a function that does CloudSearch, It’s just like CloudQuery - but for searching. 

        $("#searchForm").submit(function(){
        	var searchObj = new CB.CloudSearch('feed');
        	searchObj.searchOn('post', $('#search').val());
        	searchObj.search({
        		success: function(list){
        			$("#feed").empty();
        			if(list.length > 0){
        				for (i = 0; i < list.length; i++) { 
        					$("#feed").append("<div class='panel panel-default'><div class='panel-body'><div class='col-md-1 col-sm-2 col-xs-3'><img src='image/profile.png' class='img-responsive img-circle'/></div><div><strong>"+list[i].get('name')+"</strong><br/>"+list[i].get('post')+"</div></div></div>");
        				}
        			} else{
        				$("#feed").append("<div class='panel panel-default'><div class='panel-body'><strong>No Match Found</strong></div></div>");
        			}
        		},
        		error: function(err){
        		}
        	});
        	return false;
        });


##You're done. What's next? 

Launch the file in your local browser, and play around with the app you've just created, You can even host it somewhere and share the link.

Few things to keep in mind before you do : 

* If you need sample code of the project which we have written, Please check out  : [https://github.com/CloudBoost/sample-social-network](https://github.com/CloudBoost/sample-social-network)

* The code which was demonstrated is not secure. Anyone can delete the posts which you have created, for security - Make sure you use ACL's which is in the docs [here](https://docs.cloudboost.io/#ACL). OR You install the NodeJS / Any SDK on the server and have a REST API which talks to your client. [Here's a great post on how to create the NodeJS API on the server.](http://pixelhandler.com/posts/develop-a-restful-api-using-nodejs-with-express-and-mongoose)


You can now build any kind of apps using CloudBoost.io, this was just one of many examples. If you need more sample apps built with CloudBoost, You can check out our GitHub [here](https://github.com/cloudboost). Let us know if this helps and if it does. Please share the love. 

If you need any help, comment below or post your questions on StackOverflow and tag it with CloudBoost and We'll be happy to help. 


