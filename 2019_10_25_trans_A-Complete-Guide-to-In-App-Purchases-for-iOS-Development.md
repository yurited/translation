iOS 应用内购开发完全指南

by Gabriel Theodoropoulos, appcoda.com October 25, 2019 06:55 AM

大家好！在这个App Store里面已经满是App的时代，用户的有过剩的选择。在所有App 种类里面都有很多的竞争，用户也想前先试用再决定自己是否喜欢。另一方面，开发者的目标是使他们发布的App 创造利润，但首先他们需要创造出用户群。发布一个收费软件并不是经济上成功的保证；除非这个App实在十分卓越，用户为其买单的几率很低。令人欣慰的是，有一种方案可以满足双方的需求，用户可以先试用App，开发者也可以有利润；这个方案叫*应用内购买(In-App Purchase)*。

编者按：本指南在[原指南](https://www.appcoda.com/in-app-purchase-tutorial/).的基础上提供了全面的更新。

通过为应用程序提供[应用内购](https://developer.apple.com/in-app-purchase/)，我们（作为开发人员）可以把一些内容或功能锁定，隐藏或标记为不可用，直到用户付费。 用户这边会高兴因为他们可以体验应用程序的免费部分，如果满意，他们将愿意购买高级内容。

通过应用内购买可以购买的任何的东西都称为_商品_。App Store提供了四种不同的商品：

* _消耗品(Consumable)_：这些是消耗完之后可以再次购买的商品。
* _非消耗品(Non-consumable)_：这些是只能购买一次购买的商品。 在重装App后，用户无需再次付费，而是从App Store恢复购买(restore)这些产品。
* _自动续订(Auto-renewable subscriptions)_：用户订阅一定时间的内容或功能，到期自动续订。用户可以随时取消订阅。
* _不可续订的订阅(Non-renewing subscriptions)_：与上述类似，但不会自动续订。 购买的内容也有所不同。

在本教程中，我们不会讨论订阅。我们仅关注消耗品和非消耗品。因为在单一教程中不可能涵盖所有内容。

使用应用内购买(IAP)时，可能会有只在用户付费后才_可下载内容_。 这些内容可以存在您的或Apple的服务器中。这种情况我们也不会涉及。但我希望您在完成后可以拓展在教程中所学到的，并且根据您的所需加上缺少的东西。

集成和提供应用内购并不困难，其实只有几个步骤。 如果你以前没有做过，你可能会觉得它复杂。但请相信我它其实并不复杂。 请继续阅和读学习演示程序，然后写出你人生中第一次的应用内购功能！

关于演示程序
---

我们在本教程中搭建的演示程序，是一个_假想游戏_的一部分。 在它的（假）功能中，有三个应用内购功能（这些是真的）。用户可以：

1. 买一条命
2. 买超能力点数
3. 解锁所有游戏地图

你可以敞开脑洞来写游戏剧情！

![in-app purchase-swift demo](https://www.appcoda.com/wp-content/uploads/2019/10/t68_1_app_screen-571x1024.png)

前两个是消耗品(Consumable)：意思是消耗完之后可以再次购买。第三个是解锁所有地图，是只能买一次的非消耗品(Non-consumable)。

简单起见，游戏内容在显示在TableView中的三个Cell里。可以在上面的屏幕截图中看到，前两个是消耗品，最后一个是非消耗品。 点击前两个单元格有双重作用：

* 如果没有购买过额外的生命或超能力点数，那么点击它们就开始应用内购。
* 如果购买了额外的生命或超能力点数，则点击对应Cell之后会分别减少可用生命或超能力点数的数量，直到变为零。 然后用户可以再次购买。

如你所知，我们的目标是让应用程序内购正常工作，并使上述过程正常运行。 最重要的是，写完IAP相关代码后，我们就会有一个可以直接重用，经过少量修改就能重用的类。

你可以下载一个Starter Project。 它包含Demo App的所有部分，但是这些部分和应用内购没有直接关系。下载后用Xcode打开它。 这个Project是用Xcode 11.1创建的。

尽管Demo Project非常简单，它也是基于MVVM架构构建的，因此你可以更轻松地专注于项目的各个部分。 简单看看，并熟悉一下它的各个部分。您可能会发现在Model.swift文件的Model类中定义的GameData Struct 比较有趣。 这个Struct 的gameData实例是用来保存与购买的商品有关的数据的。 GameData实现了SettingsManageable 协议，用于在本地保存数据。该协议我们已在之前的如何使用协议管理应用程序配置的教程中讲过。

注意：存储应用内购相关数据的方式完全取决于你的App本身以及在本地保存数据的机制。我强烈建议你避免使用UserDefaults 作为本地存储数据的解决方案。

综上所述，请准备好开始学习如何使用应用内购； 从开始到结束，全程都会是有趣的探索过程！

在App Store 中准备应用内购

在开始往Xcode里写代码之前，我们要做的第一件事就是建立将通过该应用提供的应用内购的产品。这是要在App Store上完成的事，我们要在该App Store上创建一个新应用程序（或应用程序记录）。此外，在准备集成应用内购的过程中还包括其他几个步骤。

总而言之，在切换到Xcode之前我们要做的事有：

1. 创建一个新的应用程序标识符(app identifier)。
2. 同意App Store上所有需要同意的协议。
3. 为应用内购创建测试用户。
4. 在App Store上创建一个新的应用程序记录(app record)。
5. 创建应用内购。

我们一步一步的具体看。

创建一个新的应用程序标识符(app identifier)。

_应用程序标识符_(app identifier)是一个用于标识App Store上应用程序的唯一的字符串。 

它指向一个特定的应用程序，并与该应用程序的包标识符(Bundle identifier) 相关。在_General_选项卡下的_Project Editor_ 中找到它； Starter Project中是_com.appcoda.FakeGame_。

在App Store创建新的应用时，必须有一个应用标识符。 我们在浏览器中创建一个新的并且连接到你的[苹果开发者账号](http://developer.apple.com/account/)。登陆后，点击左侧选项中的*证书，ID和配置文件(Certificates, IDs & Profiles)* 链接。

在下一页中，单击“标识符(Identifiers)”项，然后单击蓝色小加号按钮创建一个新的。

它会显示给你各种标识符的列表，我们需要的是第一个，因此请单击应用程序的ID，然后点继续。

是时候该创建标识符了，请确保填写一下两个部分：

The Description of the app identifier. Feel free to provide any description you think it’s best, just respect the limitations shown right below the field. For example, what I wrote in that field is: “FakeGame App ID for IAP Demo App by AppCoda“.
Next, in the Bundle ID keep the Explicit radio selected, and copy-and-paste the Bundle identifier from Xcode to that field.

Finally, click Continue and then in the last step click Register. By going back to the list of the app identifiers, you should be able to see the one that we just created!


Fix Any Pending Agreements
Let’s leave the developer’s account now and let’s go to the App Store, also known as the iTunesConnect. If you did not sign out from the developer’s account then you’ll be automatically connected, otherwise just provide your credentials to sing in again. Here’s the place where we’ll create a new record for our application and we’ll setup the in-app purchases, as well as the place to perform a few more necessary actions. One of them is to check if you have any pending agreements that should be taken care of, like for example the next one:


Notifications like the one illustrated right above contain links to the page you should visit so you can accept the agreement(s). You can also go there on your own if you select the Agreements, Tax, and Banking options on the home screen of the App Store:


Once you get there, find the agreement or any other pending task (such as setting up a bank account) and proceed to the suggested or required actions so you eliminate all issues.


Even though it’s not necessary to have the above fixed when you’re creating and testing in-app purchases, you still have to take care about it if you’re planning to send your app either for testing to TestFlight, or to release it.

Create Test Users
Until an app is released on the App Store, all in-app purchases should be tested in a sandbox mode and neither you nor anyone else testing should pay with real money. By default, external testers who test in-app purchases through TestFlight don’t actually pay when they are asked to. However, internal testers like the developers of an app should be using test user accounts and not their real Apple ID and iCloud accounts.

Creating test users in the App Store it’s easy, however there’s a negative point: Even though we’re talking about fake accounts, real email addresses are required. A confirmation email is sent by Apple which should be validated before any test account is used!

So, if you want to have multiple test users it might be a bit of a hassle to have the same number of email addresses. I’d recommend to check if your email service provider allows to use aliases along with your normal email address (as GMail does for example). If you have a paid server service (shared hosted, dedicated, or a personal one), then things are easier for you as you can create as many temporary email addresses as you want if you don’t like using aliases, and then delete them easily.

Onto the actual process now, select the Users and Accesses option in the home screen of the App Store. In the next one, you’ll find out a section called Sandbox to the left menu, and right below a link titled Testers:


By clicking on it you’ll be taken in the test users page. There’s a blue plus button on top that you should press. You then fill in the information for the new tester.

If you’re about to create multiple users, then I’d suggest to choose different App Store territories so you can test in-app purchases with different currencies. Also, make sure to remember the password you set, because there’s no way to edit this form again. You’ll have to start over if you forget a test user’s password.

When you’re done typing the test user’s information, click on the Invite button and wait for a confirmation email to come. Repeat the process by completing a new form for each new tester account you need to add.

Created test accounts are listed as shown next. You can remove them by clicking on the Edit button on the top-right side of the window, but you cannot edit them.


Create A New App On The App Store
Let’s go now to some more juicy stuff, and let’s create a new app record on the App Store which we’ll connect to the actual iOS application. Get started by clicking on the My Apps option on the home screen of the App Store. Then, click on the Plus button on the top-left side of the top bar.

In the form that shows up, there are four fields that have to be mandatorily filled in:

The name of the app. Make sure to provide a unique app name! If you provide a name already taken you’ll see an error message when you’ll try to create the app, so just go and change it.
The primary language the app is using.
The Bundle ID. Use the drop down menu to locate the app ID we created earlier which is connected to the Bundle Identifier of the app.
The SKU – a unique string for the app not visible on the App Store.
Also, make sure to select the iOS checkbox in the Platforms section.

Right below you can see the form completed. Use it as a guide and fill it in on your side too.


If there’s no missing data and the name you provided is unique, then a new app will be created right after you click on the Create button. You’ll automatically be navigated to the app information page:


Adding In-App Purchases
The most wanted time is finally here! In this part we’ll create the in-app purchases that our app is going to offer. Before we do that, let’s recap on what exactly we’re going to provide:

A consumable in-app purchase for buying three lives to use in the game.
A consumable in-app purchase for buying two super powers.
A non-consumable in-app purchase for unlocking all game maps.
With all the above in mind, let’s start creating them. Click on the Features link in the top bar, and then make sure that the In-App Purchases is selected on the left menu options.

The main area of the screen is where in-app purchases entries are going to be listed. At the time being there’s none, so click the blue plus button to add one. The following popup will ask you to select the kind of in-app purchase you want to create, so click on the Consumable radio button and then on Create.


A new empty form to fill in is appearing once again. Let’s go through its fields and what they’re all about:

Reference Name: It’s the name of the in-app purchase on the App Store, but it’s for internal use only. It won’t be shown to the users so don’t worry too much about the value you will provide here. However, give a name that makes clear what this in-app purchase regards. For example, “Extra Lives” (without the quotes) is a good name to make us understand that this one is about the additional lives a user can buy in the game.
Product ID: This must be a unique string (alphanumeric as Apple says) that will be used for reporting, but here’s a recommendation: Use the app’s bundle identifier as a prefix to the ID value you will specify here. That way you ensure that it’ll always be unique. In our case, “com.appcoda.fakegame.extra-lives” (without quotes) is a unique product ID. Important: Note the product IDs you create here somewhere, we’re going to need them later on.
Cleared for Sale: Keep it always selected if you want the in-app purchase to be available to the public.
Pricing: Select the price you want for your in-app purchase. Since this is just a demo, choose any price you would like from the drop down menu. Scroll to bottom to find alternative prices as well.
App Store Information – Display Name: This is the name of the in-app purchase as it will be shown to users in the app. Note this: For each supported language in your app, you should provide a localized version of this and the next field as well. The value I set here for the first in-app purchase is “Get Extra Lives“.
App Store Information – Description: A description of the in-app purchase publicly shown, but also optional to be presented by the app. I would recommend to always show it to your users so they can get more details on what they’re about to purchase. For example: “Acquire three (3) additional lives!“.
Here it is completed:


By scrolling to the bottom of the page you’ll notice two more sections lying there:

App Store Promotion (Optional): By default, right below the app’s name on the App Store there will be a string saying something like: “Free – Offers In-App Purchases”. However, if you want to advertise the offered in-app purchases in the app’s page on the App Store, then provide a promotional image as described by Apple.
Review Information: This is not required when implementing and testing in-app purchases, but it’s required when an in-app purchase is about to be reviewed either for releasing it to the App Store along with the app, or for TestFlight testing. Review Notes are not mandatory to be provided, however a Screenshot is needed. You can take a screenshot of the app where in-app purchases are offered and upload it, it’ll be suffice. For now, however, leave it empty; we can proceed without it.
When you finish providing the in-app purchase details, click on the Save button you will find on the top-right side of the form. Then go back and start creating the second in-app purchase. Select a consumable IAP again, and fill in the form using the following information:

Reference Name: Super Powers
Product ID: com.appcoda.fakegame.superpowers (change it according to your own Bundle ID)
Price: Any price you want
Display Name: Additional Super Powers
Description: Get two (2) additional super powers!

Save this in-app purchase, and then create the last one. This time, select a non-consumable one:


On the contrary of the previous two, this is a kind of purchase that each user will make once. However, this doesn’t change the way it’s being set up. The exact same kind of data must be given in this case too:

Reference Name: Unlock All Maps
Product ID: com.appcoda.fakegame.unlock_maps (change it according to your own Bundle ID)
Price: Any price you want
Display Name: Unlock Maps
Description: Unlock all maps in the game forever!

The in-app purchases that our app will be offering are now ready and you can find them all together listed in the IAP home page:


You’ll notice that the Status of all in-app purchases is set to Missing Metadata. That’s because we didn’t set a review image to any of them. Don’t worry though, by adding a review image and saving the status will change to Waiting For Review. Such an action isn’t necessary here; we’re not going to publish this demo app.

Using Product IDs In Xcode

Finally, all necessary preparation has come to its end. It’s now time to leave App Store and go to the starter project in Xcode. The final goal in this post is to create a reusable class that will manage in-app purchases, but this class will need to know about the available product identifiers created on the App Store. So, let’s start with that and let’s add all IAP product identifiers to a special file you’ll find in Xcode under the In-App Purchases group, called IAP_ProductIDs.plist.

The purpose of this file is to let us keep product identifiers gathered in one place, in a simple and code-unrelated fashion. The class we’ll implement next will get all product identifiers by just reading the contents of this file.

So, open IAP_ProductIDs.plist in Xcode and make sure that the type of the Root item is set to Array. Then, add three items one after another, and each time copy and paste the product ID of a different in-app purchase created earlier. In the end you should end up with this:


Start Implementing The In-App Purchases Managing Class

Let’s focus on implementing the reusable class now that will manage in-app purchases in our demo app and not only. In the starter project in Xcode, open the file called IAPManager.swift which you’ll also find under the In-App Purchases group in Project Navigator. It’s currently empty, but we’ll change that here and in the following parts.

The first move is to import the StoreKit framework; it’s the one that will allow us to deal with all in-app purchase related concepts and entities in programming level. Right after the first import statement add the following:

Let’s declare the new class now which will have the same name to the file: IAPManager. Leave a couple of empty lines and add this:

Note: Later on the IAPManager class will adopt a protocol called SKPaymentTransactionObserver. This protocol requires any conforming types to also conform to NSObjectProtocol, and that’s something we can do with no hassle simply by just making IAPManager a subclass of the NSObject class.

In order to keep things simple and to avoid potential troubles by having multiple instances of this class, we’ll apply the Singleton pattern and we’ll be using one instance only, the shared instance. Adopting the Singleton pattern requires two things:

To have an instance of the class initialized as a static property.
Keep the initializer private so no more instances of the class can be created anywhere in the app.
Here it is:

As you will see next, there will be cases where the various operations won’t return the desired or expected results. It’s important to treat these cases gracefully and make IAPManager class indicate them properly in any custom type using it. For that purpose, we’re going to create the following enum with a few custom errors:

Make sure to add the enum inside the class body. The meaning of them:

noProductIDsFound: It indicates that the product identifiers could not be found.
noProductsFound: No IAP products were returned by the App Store because none was found.
paymentWasCancelled: The user cancelled an initialized purchase process.
productRequestFailed: The app cannot request App Store about available IAP products for some reason.
Along with the above enum, it’s necessary to add the following extension right after the closing of the IAPManager class. In it, the localized descriptions of the custom errors are specified:

Reading Product Identifiers

Before we perform any action related to in-app purchases, it’s necessary to get the product identifiers that we previously added to the IAP_ProductIDs.plist file. For that purpose, we’ll implement a small helper method that will do just that: Reading the property list file from the app bundle and returning the product identifiers as an array of String elements.

The method is marked as fileprivate because we want it to be visible in this file only. There’s no reason to be accessible by other entities out of this class. However, remove the fileprivate keyword if you ever need to have the product identifiers available somewhere else besides here.

The first step in this method is to get a URL pointing to the IAP_ProductIDs.plist file in the app bundle:

Always make sure to type the file name and its extension correctly when using the above method; a typo error is enough to make you lose time and wonder why the file cannot be found even though it exists in the project. If the file is found, its URL is assigned to the url property.

Next, we’ll load the file contents to a Data NSObject:

Initializing a Data object as shown above can throw an exception, so including it in a do-catch statement is necessary. In case the file cannot be read and the data property to be initialized, then we print the description of the error occurred, and we return nil.

Since the above data object was created by using the contents of a property list file, we’ll decode it and convert it to an array of String elements with the help of the PropertyListSerialization class; it allows to encode to and decode from property list objects. If you’re familiar to the JSONSerialization class, then this one is the equivalent for property lists, not JSON objects.

Still inside the do-catch statement, let’s convert the loaded property list data into an array as shown right below:

In case it’s not possible to convert to a collection of String values, then we just assign an empty array to productIDs.

Finally, we need to return it:

Here’s the whole method:

Requesting App Store For Available IAP Products

Making IAPManager capable of loading the product IDs, the next step is to fetch all available products offered for purchase from the App Store. This action will return a collection of SKProduct objects, where each one describes an in-app purchase and contains its details as it was configured on the App Store.

We’ll start by defining a new method:

Let’s talk a bit about the parameter of this method, and let me start with a question: Are you aware of the Result type that was first-introduced in Swift 5? Citing the official documentation:

A value that represents either a success or a failure, including an associated value in each case.

In simple words, this type makes it easy to return the outcome of an operation and to indicate whether it was successful or not. On success, it’s possible to carry any necessary custom data; on failure it carries the error that caused the operation to fail.

In our case, our Result value will carry the collection of fetched products from the App Store if we get them successfully, and a custom error type (IAPManagerError) on failure.

Fetching IAP products from the App Store is an asynchronous process. That means that the above method cannot return the products fetching result instantly. So, the parameter of the method has to be a closure (or a callback handler in other words) which will be called when StoreKit notifies our class that has got a response from the App Store. The parameter of that closure is the Result value as shown in the method definition above.

The above borns a new requirement now: To declare a property in the IAPManager class which will keep a reference to the handler (closure) even when the execution of the getProducts(withHandler:) method is finished. Go to the beginning of the class in the properties declaration area and add the following:

Back to the getProducts(withHandler:) method now. We start implementing it by assigning the productsReceiveHandler parameter value to the onReceiveProductsHandler property so we can call it at any time later in the future:

Next, let’s get the collection of the identifiers for the products that we want to fetch data for. For this purpose, we’ll make use of the getProductIDs() method we implemented earlier:

Remember that it’s possible for the above to return nil. If that happens, then the wanted product identifiers could not be read for some reason and we should return from this method immediately. However, it’s necessary to notify the caller of the class about the error that occurred. We call the productsReceiveHandler handler and we pass a Result type with a failure indicating the exact error happened.

Note: If you find yourself being uncomfortable by the syntax of the Result type, then I’d recommend to search for more usage examples on the Internet, as well as to play with it in a playground until you master it.

Continuing to the implementation, let’s initialize a products request for the App Store:

The initializer shown above awaits for a Set of product identifiers, not an array. That’s why we initialize a new Set using the productIDs array.

As mentioned already, requesting the App Store is not a synchronous operation. The result of it, meaning the response from the App Store, is available through a couple of methods provided by the SKProductsRequestDelegate and SKRequestDelegate protocols. IAPManager class will adopt them, but first it must be set as the request’s delegate:

We are now ready to make the request:

The getProducts(withHandler:) method is a quite important one, and even though it’s small, it contains vital steps for making in-app purchases possible. Here it is all together:

Handling App Store Response

Right above we set the IAPManager class as the delegate of the SKProductsRequest object (the request). Now, it’s mandatory to adopt the SKProductsRequestDelegate protocol and implement at least one required method.

Go after the closing curly bracket of the IAPManager class, and add the following extension:

In it we’ll implement the following method which gets called when the App Store sends back a response to the original request:

The first parameter value regards the request that triggered the response. The second (the response) is what we really care about here, as it contains a collection of SKProduct objects; the available content for purchase. Each product contained in the response matches to a single product identifier in the list of identifiers existing in our property list file.

Going into the logic that we’ll apply here, at first we’ll get the collection of products:

Then we’ll check if there are any products returned or not. If so, we’ll call the onReceiveProductsHandler and we’ll pass a Result value indicating a success and supplying it with the array of products:

Among others, the above illustrates how a Result value with the case of success is being constructed.

In case there are no products returned, we’ll call the onReceiveProductsHandler once again, but this time the Result value will indicate an error, and we’ll pass the noProductsFound custom error as its associated value:

Even though finding no products is not necessarily an error (when, for example, there are no IAP entries on App Store or the given product identifiers do not match to any IAPs), treating it the way shown above ensures that the two cases of having and not having products will be handled separately by the caller of this class.

Here’s the entire method:

Note: The response object provides a property called invalidProductIdentifiers. It’s a collection of identifiers regarding products that are not valid to be purchased. Although we do not use it here, keep it in mind in case you ever need it.

What the above method does not do is to let us know if the request failed for some reason. We can deal with that by implementing another delegate method:

Regardless of the error that caused the request to fail, it’s necessary to let the IAPManager caller know about it. For one more time we’re going to call the onReceiveProductsHandler closure passing the productRequestFailed custom error.

Finally, there’s one more delegate method which can be optionally implemented:

We won’t use it here, but be aware of it in case there’s any additional custom logic you want to add.

Getting Product’s Price As A String

In the next part we’re going to have a first taste about everything we’ve done so far, but before that it’s necessary to implement a small assistive, yet necessary method in the IAPManager class. Its purpose is to return a product’s price as a formatted currency string. The implementation shown right next is taken as-is from Apple’s documentation but it’s not hard to understand it:

Notice that the string is created based on the product’s localized price value which is taken by accessing the priceLocale property.

Testing What Is Built So Far

Let’s take a small break from building the IAPManager class now, and let’s make a few updates to other parts of the app so we can run it and test what we’ve done so far. There are two things that we’ll do here:

We’ll fetch the available in-app purchases from the App Store (the products).
We’ll display product information while trying to initiate a buy process.
Note: Since the goal of this post is to focus on the creation of a reusable component to perform in-app purchases, I’ll save us some time by being less thorough when presenting missing parts of the app which are not related directly to the IAPManager class. Please take your time and have a good look at the project if you need so.

Fetching IAP Products
Before users are able to make any in-app purchase, the app must fetch all information about available IAPs from the App Store. There’s no rule when exactly apps should do that, it always depends on each specific app itself. Sometimes it’s suitable to fetch products right before an in-app purchase is started. Some other times it’s better to fetch them right after the app has launched.

In this demo application, and given the fact that we have one view controller only, we’ll fetch the available IAP products right after the view of the view controller has appeared and it’s ready to be used. If you check the viewDidAppear(_:) method in the ViewController.swift file, you’ll see that it calls the viewDidSetup() method of the ViewModel instance; that is place where we’ll initiate the product fetching.

Open the ViewModel.swift file and locate the viewDidSetup() method. When there are no products fetched recently by the App Store, no in-app purchase process can start, so users have to wait a bit. Let’s do that, and let’s trigger the appearance of an overlay view with an activity indicator in it while the app “talks” to the App Store and requests for any in-app purchases:

Now we can use the getProducts(withHandler:) we implemented earlier in the IAPManager class. Remember that IAPManager is a singleton class therefore we access the method through its shared instance:

We can remove the overlay view shown in the app when the fetching process is finished and the above closure gets called. Just remember to do that on the main thread.

Let’s handle the result now. If it’s a success, then it’ll contain the fetched IAP products; if it’s a failure it’ll carry the error that caused it. In the first case we keep the fetched products in the products property of our model; in the second we trigger the appearance of an alert that will display the localized description of the returned error:

Find the implementation of the showIAPRelatedError(_:) delegate method in the ViewController.swift file to find out how the error is being displayed.

Run the app now if you want (either in Simulator or on a device) and look the overlay view with the spinner appearing while the app is fetching the in-app purchase information from the App Store. If it just disappears after a few moments, then products have been successfully fetched and kept by the app. If any error occurs, you’ll see an alert showing up with the error description.

Here’s the entire method we just implemented:

Note: The way you’ll temporarily keep fetched IAP products in your app is totally up to you. Here I chose to create the products property in the Model class, which is an array of SKProduct elements.

Displaying Product Information
By having the available in-app purchase products at the app’s disposal, we can use them for presenting certain information to users when a purchase process is about to start. To be specific, we’ll be displaying the following for each product a user wants to purchase:

The localized title.
The localized description.
The price.
Remember that all of them have been set on the App Store. And here’s something to avoid: Do not hardcode any title, description or price into the app; all these can change at any time if you edit your in-app purchase records on the App Store.

Let’s focus on our demo app again now. We will initiate a purchase process when a user taps on a cell and any of the following is true:

The first cell is tapped and there are no extra lives available.
The second cell is tapped and there are no super powers available.
The third cell is tapped (Note: Third cell won’t be visible after unlocking all maps by buying the matching product; it regards a non-consumable product).
This logic is already applied in the tableView(_:didSelectRowAt:) table view delegate method in the ViewController.swift file. Go there and take a look now. When any of the above conditions is true, the getProductForItem(at:) method of the ViewModel is called in order to return the matching product to the tapped cell. That product is given as an argument to the showAlert(for:) method defined in the ViewController class.

The purpose of the showAlert(for:) method is to display an alert controller which will contain all the product details mentioned above right before an in-app purchase is made. It actually displays an alert that asks users if they want to continue with the purchase of the described item.

Note: I would recommend against of using a default alert controller for displaying a product’s information, unless your app is implements a really basic UI. You’d better describe products in custom designed views that match to the look and feel of your app.

Locate the showAlert(for:) method in the ViewController.swift file. We’ll start implementing it by getting the localized price of the product given as an argument:

Let’s initialize an alert controller now. We’ll display the product’s title as the alert’s title, and the product’s description as the alert’s message:

The product’s price will be used as part of the title of an alert action that will prompt users to buy the item. Here it is:

The button’s title will say something: “Buy now for $0.49”. When users tap on it, the actual purchase process will be initiated, but that’s for later. For now we just leave a “TODO” comment.

Finally, let’s add a cancel action to the alert, and let’s present it:

Here’s the entire method:

1
2
3
4
5
6
7
8
9
10
11
12
13
func showAlert(for product: SKProduct) {
guard let price = IAPManager.shared.getPriceFormatted(for: product) else { return }
let alertController = UIAlertController(title: product.localizedTitle,
message: product.localizedDescription,
preferredStyle: .alert)
alertController.addAction(UIAlertAction(title: "Buy now for \(price)", style: .default, handler: { (_) in
// TODO: Initiate Purchase!
}))
alertController.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))
self.present(alertController, animated: true, completion: nil)
}
Run the app now and tap on any cell. You should see the alert we just created is showing up and describing the selected product! Of course no purchase can take place now, and that’s something that we’re going to change right now!


Implementing Purchases

In programming level, a purchase is a transaction (aka a SKPaymentTransaction object), and responsible for managing transactions is a class called SKPaymentQueue. The payment queue is the one which communicates with the App Store and handles all the payment process. It’s also responsible for presenting the built-in UI that appears when performing purchases.

Every app that wants to offer in-app purchases is required to add a payment transaction observer to the queue (a SKPaymentTransactionObserver object) so it’s capable of catching any updates regarding a purchase. As you understand, making purchases is an asynchronous process just like fetching the products information from the App Store. The outcome of a transaction should always become absolutely clear to users, regardless of whether it was successful or not.

Observing The Payment Queue
So, with that new information in mind, let’s go back to the IAPManager.swift file where we’ll implement two new small methods. With them we’ll be adding and removing a payment transaction observer to the payment queue respectively:

Just implementing the above two is not enough. It’s also necessary to call them appropriately so the app can start and stop observing for payment transaction updates as needed.

Open the AppDelegate.swift file. In the application(_:didFinishLaunchingWithOptions:) method make call to startObserving() so the app starts observing each time is launched:

Next, implement the applicationWillTerminate(_:) to stop observing:

That’s it. The app is now capable of watching properly for updates in the payment queue, and we are ready to move forward and make it possible to create new transactions (meaning to make new purchases), as well as to handle any transaction related updates coming from the App Store.

Note: Don’t be bothered by the couple of errors currently shown by Xcode; they’ll be fixed soon.

Checking For IAP Eligible Device
Before an app triggers a purchase process, it’s important to know whether in-app purchases are actually allowed or supported on a device. It’s possible, for example, that parents have turned off IAPs through the device settings. That’s a state that apps must always check for and notify users accordingly.

The information of whether in-app purchases can be made is something that’s taken from the payment queue once again. Go to the IAPManager.swift file and implement the following method:

As you see, it’s as simple as that. We will call canMakePayments() method later on, when we’ll keep adding the missing parts from the demo app that will make purchases possible.

Making Purchases
With the payment queue being observed and with the ability to know whether a device can actually make payments or not, let’s go to the implementation of a new method that triggers a new purchase:

This method accepts two arguments:

The first one is the actual product (a SKProduct object) that is about to be purchased.
The second parameter is a callback handler ( a closure) similar to the one we met in the getProducts(withHandler:) method. It has a Result type as a parameter with a boolean value as the associated value for the success case, and an error object as the associated value for the failure case. This callback handler is necessary because making a payment is an asynchronous operation; it’ll be called when the payment process is finished.
Inside the method now, we initialize a payment object (SKPayment) using the given product. Then, we add the payment object to the payment queue and we’re done! Once a new payment object is added to the payment queue, things will start working without any other action on our behalf, so we just have to wait for the entire process to come to its end.

The callback handler that is given as an argument to this method should be kept in a class property, so it’s available when the method’s execution is finished. This is what’s happening in the last line above; the handler parameter value is assigned to the onBuyProductHandler. However, the second one does not exist in the IAPManager class yet.

Go to the properties declaration area of the class and add the following line:

Handling Transaction Results
Now that we made it possible to make a payment, let’s handle the results of a transaction. For this purpose, we need to adopt the SKPaymentTransactionObserver protocol and implement a required method.

Go after the closing curly bracket of the IAPManager class once again, and add one more extension:

In it we’ll implement the following method:

This method is called when the state of a transaction changes. The second parameter value, transactions, is an array of SKPaymentTransaction objects as multiple transactions can be running at the same time. We’ll start with that and we’ll go through each transaction:

Next, we’ll add a switch statement which we’ll use to examine the various states of each transaction:

What we are really interested about is the first three cases:

purchased: When a payment is finished and the product has been purchased.
restored: When a previously purchased item has been restored from the App Store.
failed: When a transaction fails.
In the first case where a purchase is successful, we’ll call the onBuyProductHandler closure passing true as the value of the result object. This will notify the IAPManager‘s caller about the successful outcome. On top of that, there’s one more action that’s mandatory to be done; to indicate to the payment queue that it can now consider the transaction as finished.

Actually, the last line is something we’ll also do in the next cases too.

We’ll postpone the restored case for a bit later. For now add this:

In the failed case, we’ll check at first if the transaction’s error object is nil or not. A transaction’s error is a SKError object and it has a specific code that describes the error. There’s a special one called paymentCancelled and it’s happening when a user cancels a purchase right before the actual payment is done. If this is the case, then we’ll pass a failure value to the result with the custom IAPManagerError.paymentWasCancelled error. If not, then we’ll pass failure with the actual error that took place. That way we’ll make it easy for the app to know whether the payment failed because of the user or not.

Implementing the capability to make in-app purchases is now complete. Time to try it out!

Enable App To Make Purhases

Open the ViewModel.swift file where you’ll find a defined, yet still empty method called purchase(product:). In this one we’ll add the missing logic which will make use of what we just did in the previous parts and ultimately make payments come true for our demo app.

As you can see the returned value from the method is a boolean one. The only case where this method will return false is going to be when no payments can be made on a specific device. Otherwise it’ll trigger the new payment using the product provided as an argument and it’ll return true. Let’s see that:

Here’s the place where we make use of the canMakePayments() method which we implemented earlier.

Focusing on the else case now, we’ll indicate to the View part of our MVVM based app that a long process is about to start. This will make the overlay view with the activity indicator appear:

Let’s trigger the purchase:

We’re calling the buy(product:) method of the IAPManager class passing the product parameter value given to the purchase(product:) as its argument. The above closure is the one assigned to the onBuyProductHandler property in the IAPManager class. It’ll be called back when the purchase will have either finished successfully or not; it’s the result value to tell that.

Since the purchase is an asynchronous operation taking place on the background, whatever we’ll do in the above closure must be done on the main thread. After all, we’re going to initiate UI changes and that’s something that cannot be done using a background thread.

By calling the didFinishLongProcess() delegate method we make the overlay view disappear. Next, we examine the result value. On success we call the updateGameDataWithPurchasedProduct(_:) method which is already implemented in the ViewModel class. This is the method which, depending on the purchased product, will add extra lives or extra powers to our fake game (consumable products), or it will mark all maps as unlocked (non-consumable). Go and take a look at updateGameDataWithPurchasedProduct(_:).

If the purchase fails, then we let our view controller know about that and therefore show the error message through the showIAPRelatedError(_:) delegate method. Note that the way a purchase’s results will be handled is totally depending on the app that’s being developed, the logic applied to it, the design, and so on. So, what you see above is something that has a meaning to this demo app only. However, this doesn’t mean you cannot act similarly in your own apps; feel free to use this method as a guide.

Here’s the purchase(product:) all together:

Open the ViewController.swift file now and search for this comment: // TODO: Initiate Purchase!. You should find it in the action of the alert controller that displays the product information that is about to be purchased (showAlert(for:) method). Replace that comment and update the action as shown next:

If the purchase(product:) returns false, then the device cannot make payments and we’re showing an alert to let users know about that. Otherwise, things will flow as we built it earlier.

Testing In-App Purchases

In order to test in-app purchases you’ll need a real device. Payments cannot be done using a Simulator. However before you begin make sure to sign out from your iCloud account if you’re already connected in your device. We want all tests to take place on a sandboxed environment.

After you sign out, do not connect to iCloud using a test account. This is not the place to do it! You’ll be asked to sign in when you initiate a purchase process. However, in case you want to set the test account you’ll use in advance, or if you want to change the test user later, then:

Open Settings.
Go to iTunes & App Store.
Scroll to bottom. There’s a section called Sandbox Account. Tap on Sign-In and provide the test user credentials.
Note: Do not provide a real Apple ID here!

In addition to the above, you might want to disallow in-app purchases in your device so you can test that case too. In order to do that, go to Settings and then:

Screen Time
Content & Privacy Restrictions
iTunes & App Store Purchases
In-app Purchases
Select Don’t Allow
To enable IAPs again, follow the same steps but at the end select Allow.

Now that you know how to assign or change a test user, and how to turn on and off in-app purchases, compile the app and let it run on your device. Tap on any fake product you want to buy. For example, tap on the extra lives cell and then on the Buy button. Provide the test user’s credentials and confirm the payment. Once it’s successfully done, you’ll see that you have three available lives which you can consume by tapping on the cell. When you’re out of lives, you’ll be asked to buy again.

Note: Pay attention to the system alerts that are showing up. While confirming the purchase you should be seeing the [Environment: Sandbox] indication. If you don’t see that, then you’re not making the purchase using a test account and you should not proceed until you change that.


In case you start the purchase but you cancel it before confirming the buy, here’s what you’ll see:


Note that you can keep buying extra lives and super powers for as long as you want when running out of them. However, unlocking all maps can be purchased just once; it’s a non-consumable product, and when you do so the last cell will no longer be appearing on the table view.

Restoring Purchases

Even though we made it possible to perform in-app purchases and buy digital products through our app, there’s still one thing missing. That is the option to restore previously purchased non-consumable products.

When users pay once for a non-consumable product (like unlocking all maps in our demo app), they should never be asked to pay for it again. However, they should be able to get their purchase back when they install the app on a different device or when they re-install on the same one. Apple requires a restore button to exist in our UI which should be clearly visible and understandable by users. Without the option to restore, get ready to see your app being rejected from the App Store.

Thankfully, App Store keeps record of purchases made on non-consumable products and it will prevent users from paying again for something they’ve already bought. That means that even if someone tries to buy again a non-consumable product will fail; Once StoreKit and the payment queue get the information from the App Store that there is a previous payment, the new process will be considered as a restore and the user won’t be charged for it.

Restoring purchases is something we’re still missing in the IAPManager class, so let’s take care of it now. Open the IAPManager.swift file and add the following new method:

Let’s start with the parameter value. Once again it’s the (familiar) callback handler we also had in the buy(product:) method too, so we assign it to the onBuyProductHandler class property for future use.

You might be wondering what the totalRestoredPurchases variable is all about. When having restorable products, it’s good to know whether all of them were restored or not, and by using such a counter you can easily verify it. You’ll see how it’ll be used in a while, but first, declare it in the IAPManager class along with the other class properties:

Finally, by calling the restoreCompletedTransactions() method of the payment queue we’re triggering the process of restoring previous purchases.

Note: Restoring previous purchases regards only non-consumable products. Consumable products are not restored; users might have or might have not consumed them and it’s up to you as a developer to synchronize that information among devices if necessary using cloud solutions (such as iCloud, your server, Firebase, etc).

Now, go to the paymentQueue(_:updatedTransactions:) delegate method in the SKPaymentTransactionObserver extension. If you remember, there’s the restored case there which we had left unmanaged. Since in this method we go through all available transactions, we’ll increase the totalRestoredPurchases value by one each time we have a restored one. At the end we’ll know how many purchases were restored and how many weren’t. Here’s how the restored case should look:

Notice that calling the finishTransaction(_:) method is necessary so the transaction is marked as finished in the payment queue.

We’ll implement two more delegate methods now in the SKPaymentTransactionObserver extension’s body. The one you’ll see right next is called whenever restoring previous purchases has finished successfully, meaning without any errors, even if there are not any products to restore.

Two things to mention here: Firstly, see that in both cases we call the onBuyProductHandler passing success as the result’s value. The associated value of the success cases varies depending on whether we had restored products or not. Secondly, the way totalRestoredPurchases property is used here is something you can change and make it fit to the needs of your own app. In this demo app it’s sufficient to know that totalRestoredPurchases != 0, since we have one non-consumable product only and this condition ensures that it’s restored successfully. If you have more than one restorable products, you might need to change it and calculate the products that were not restored.

The second delegate method is invoked by the payment queue on a failed restore process. Note that a reason for the failure could be users themselves by just cancelling the entire process. You’ll find it familiar as we’re doing the exact same thing as we did in the failed case in the paymentQueue(_:updatedTransactions:) method. Here it is:

And that concludes the implementation of the IAPManager class! It’s about time to run our last test.

Enabling App To Restore Purchases

Open the ViewModel.swift file and go to the restorePurchases() method. This one is called by the restorePurchases(_:) IBAction method in the ViewController class. Similarly to what we did before, we’ll indicate a long process to the view controller, and then we’ll initiate the restore process:

When the closure gets called, we’ll examine the success value of the result. If it’s true, then restoring the product was successful and we can unlock all maps on our fake game. If not we’ll display an appropriate message, which is also the case when restoring fails.

See that we treat separately the case where there are no products to restore (the didFinishRestoringPurchasesWithZeroProducts() delegate method is called). You’re not required, but recommended to do so in your apps as well so you can notify your users properly.

In order to test the restore functionality, first buy the “unlock all maps” in-app purchase and then delete the app from your device. Install it again through Xcode and then tap on the Restore Purchases button using the same test user you used before.


Summary

Offering in-app purchases through an app is not a complicated process, but it involves a lot of steps. Starting with the preparation on the App Store and finishing by adding the last touch in your code consists of a series of actions that must be done really carefully. The detailed parts presented in this post show the way to integrate in-app purchases in your app, and by leaving here you can keep the IAPManager class as a reusable component which you can extend according to your needs. Always make sure to be in align with the Apple’s recommendations and best practices regarding in-app purchases. With that said it’s about time to let you rest because you’ve just gone through a long tutorial, which I hope you found useful. Thanks for reading and… happy earnings!

For the full source code, you can refer to this project on GitHub.

Credits: Images by icons8

Author @gabtheodor
Gabriel has been a software developer for about two decades. He has long experience in developing software solutions for various platforms in many programming languages. Since middle-2010 he has been developing almost exclusively for iOS. Tutorials consist of the best way to share knowledge with people all over the world. Follow Gabriel at Google+ and Twitter.