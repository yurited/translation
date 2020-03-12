iOS 应用内购开发完全指南

by Gabriel Theodoropoulos, appcoda.com October 25, 2019 06:55 AM

大家好！在这个App Store里面已经满是App的时代，用户的有过剩的选择。在所有App 种类里面都有很多的竞争，用户也想前先试用再决定自己是否喜欢。另一方面，开发者的目标是使他们发布的App 创造利润，但首先他们需要创造出用户群。发布一个收费软件并不是经济上成功的保证；除非这个App实在十分卓越，用户为其买单的几率很低。令人欣慰的是，有一种方案可以满足双方的需求，用户可以先试用App，开发者也可以有利润；这个方案叫*应用内购买(In-App Purchase)*。

编者按：本指南在[原指南](https://www.appcoda.com/in-app-purchase-tutorial/)的基础上提供了全面的更新。

通过为应用程序提供[应用内购](https://developer.apple.com/in-app-purchase/)，我们（作为开发人员）可以把一些内容或功能锁定，隐藏或标记为不可用，直到用户付费。 用户这边会高兴因为他们可以体验应用程序的免费部分，如果满意，他们将愿意购买高级内容。

通过应用内购买可以购买的任何的东西都称为 _商品_。App Store提供了四种不同的商品：

* _消耗品(Consumable)_：这些是消耗完之后可以再次购买的商品。
* _非消耗品(Non-consumable)_：这些是只能购买一次购买的商品。 在重装App后，用户无需再次付费，而是从App Store恢复购买(restore)这些产品。
* _自动续订(Auto-renewable subscriptions)_：用户订阅一定时间的内容或功能，到期自动续订。用户可以随时取消订阅。
* _不可续订的订阅(Non-renewing subscriptions)_：与上述类似，但不会自动续订。 购买的内容也有所不同。

在本教程中，我们不会讨论订阅。我们仅关注消耗品和非消耗品。因为在单一教程中不可能涵盖所有内容。

使用应用内购买(IAP)时，可能会有只在用户付费后才_可下载内容_。 这些内容可以存在你的或Apple的服务器中。这种情况我们也不会涉及。但我希望你在完成后可以拓展在教程中所学到的，并且根据你的所需加上缺少的东西。

集成和提供应用内购并不困难，其实只有几个步骤。 如果你以前没有做过，你可能会觉得它复杂。但请相信我它其实并不复杂。 请继续阅和读学习演示程序，然后写出你人生中第一次的应用内购功能！

关于演示程序
---

我们在本教程中搭建的演示程序，是一个_假想游戏_的一部分。 在它的（假）功能中，有三个应用内购功能（这些是真的）。用户可以：

1. 买一条命
2. 买超能力点数
3. 解锁所有游戏地图

你可以敞开脑洞来写游戏剧情！

![in-app purchase-swift demo](https://www.appcoda.com/wp-content/uploads/2019/10/t68_1_app_screen-571x1024.png)

前两个是_消耗品(Consumable)_：意思是消耗完之后可以再次购买。第三个是解锁所有地图，是只能买一次的非消耗品(Non-consumable)。

简单起见，游戏内容在显示在TableView中的三个Cell里。可以在上面的屏幕截图中看到，前两个是消耗品，最后一个是非消耗品。 点击前两个单元格有双重作用：

* 如果没有购买过额外的生命或超能力点数，那么点击它们就开始应用内购。
* 如果购买了额外的生命或超能力点数，则点击对应Cell之后会分别减少可用生命或超能力点数的数量，直到变为零。 然后用户可以再次购买。

如你所知，我们的目标是让应用程序内购正常工作，并使上述过程正常运行。 最重要的是，写完IAP相关代码后，我们就会有一个可以直接重用，经过少量修改就能重用的类。

你可以下载一个Starter Project。 它包含Demo App的所有部分，但是这些部分和应用内购没有直接关系。下载后用Xcode打开它。 这个Project是用Xcode 11.1创建的。

尽管Demo Project非常简单，它也是基于MVVM架构构建的，因此你可以更轻松地专注于项目的各个部分。 简单看看，并熟悉一下它的各个部分。你可能会发现在Model.swift文件的Model类中定义的GameData Struct 比较有趣。 这个Struct 的gameData实例是用来保存与购买的商品有关的数据的。 GameData实现了SettingsManageable 协议，用于在本地保存数据。该协议我们已在之前的如何使用协议管理应用程序配置的教程中讲过。

注意：存储应用内购相关数据的方式完全取决于你的App本身以及在本地保存数据的机制。我强烈建议你避免使用UserDefaults 作为本地存储数据的解决方案。

综上所述，请准备好开始学习如何使用应用内购； 从开始到结束，全程都会是有趣的探索过程！

在App Store 中准备应用内购

在开始往Xcode里写代码之前，我们要做的第一件事就是建立将通过该应用提供的应用内购的产品。这是要在App Store上完成的事，我们要在该App Store上创建一个新应用程序（或应用程序记录）。此外，在准备集成应用内购的过程中还包括其他几个步骤。

总而言之，在切换到Xcode之前我们要做的事有：

1. 创建一个新的应用程序标识符(app identifier)。
2. 同意所需协议
3. 创建测试用户
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

1. 应用程序标识符的**说明(Description)**。 请提供你认为最好的描述，遵守下方显示的规则即可。 例如我在该字段中写的内容是："_FakeGame App ID for IAP Demo App by AppCoda_"。

2. 接下来，在**包ID**中，选中**直接(Explicit)**，然后**把包标识符(Bundle ID)从Xcode复制并粘贴**到此处。

![A Complete Guide to In-App Purchases for iOS Development 4](https://www.appcoda.com/wp-content/uploads/2019/10/t68_7_register_app_id-1024x293.png)


最后，单击 _继续_ 并且在最后一步单击 **注册(Register)**。回到应用程序标识符列表后，你应该可以看到我们刚刚新建的这一项！

![A Complete Guide to In-App Purchases for iOS Development 5](https://www.appcoda.com/wp-content/uploads/2019/10/t68_8_listed_app_id-1024x293.png)

### 同意所需协议

我们离开开发者的帐户页，然后到App Store，或者说是[_iTunesConnect_](https://appstoreconnect.apple.com)。 如果你没有登出开发者帐户的话就会自动登陆，否则你需要自己输入账号密码来登陆。 在这里，我们将为我们的应用程序创建新的记录，并为其设置应用程序内购，以及做其他一些必要的操作。 其中一项是检查你是否有任何 _待处理协议(pending agreements)_，例如下面的协议：

![A Complete Guide to In-App Purchases for iOS Development 6](https://www.appcoda.com/wp-content/uploads/2019/10/t68_9_pending_agreement-1024x146.png)

像上面这样的提醒包含了你需要阅读和同意的协议的链接。你也可以自己在App Store主页面选择 **(协议，税务和银行业务)Agreements, Tax, and Banking**以便自己查看。

![A Complete Guide to In-App Purchases for iOS Development 7](https://www.appcoda.com/wp-content/uploads/2019/10/t68_10_agreements_option-1024x509.png)

打开后，找到协议以及任何其他待处理任务（比如设置银行账户）, 然后完成建议或者必须做的事情直到没有问题为止。

![A Complete Guide to In-App Purchases for iOS Development 8](https://www.appcoda.com/wp-content/uploads/2019/10/t68_11_agreement_to_fix-1024x143.png)

即使在创建和测试应用内购买时不必修复上面的问题，但如果你打算将应用发给TestFlight进行测试或发布，你还是需要做这些事情的。

### 创建测试用户

在App Store发布应用程序之前，所有应用内购都应以 _沙盒模式_ 进行测试，你和其他任何测试者都不需要用真钱付款。 默认情况下，通过TestFlight测试应用内购的外部测试人员在购买时也不需真正付款。 但是，像应用程序开发者这种内部测试人员应使用 _测试用户帐号_ 而不是真的Apple ID和iCloud帐号。

在App Store中创建测试用户很容易，但有一个 _缺点_：即使是 _假_ 帐户，也 _需要真正电子邮件地址_。 你需要在使用任何测试帐户之前验证Apple发送的验证电子邮件！

因此，如果你想拥有多个测试用户，拥有相同数量的电子邮件地址可能会有些麻烦。 我建议你检查你的电子邮件服务提供商是否允许将 _别名(aliases)_ 和你的普通电子邮件地址一起使用（比如 _gmail_）。如果你有付费服务器服务（共享托管服务，专用服务或个人服务），那么对你来说就更容易了，如果你不喜欢 _别名(aliases)_ 的话，你可以创造任意数量的临时邮件账号，然后将它们删除。

现在进入真正的流程，在App Store的主屏幕中选择**用户与权限(Users and Accesses)**选项。 然后，你会在左侧菜单的**测试人员(Tester)**的链接的下方找到**沙盒(sandbox)**：

![A Complete Guide to In-App Purchases for iOS Development 9](https://www.appcoda.com/wp-content/uploads/2019/10/t68_12_testers_menu.png)

点击它之后你会进入测试用户页面。 点击**蓝色加号**后你可以填写新的测试人员信息。

如果你想创建多个用户，那我建议选择不同的App Store区域，以便测试使用不同货币的应用内购。 另外，请 _记住你设置的密码_，因为这个表单无法再修改。 如果你忘了测试用户的密码就必须再创建新的。

输入完测试用户信息后，点击 _邀请(Invite)_ 按钮，之后会有确认电子邮件发过来。重复这个过程就可以为您需要添加的每个测试人员帐户填写表格。

创建的测试帐户如下所示。 您可以通过单击窗口右上角的 _编辑(Edit)_ 按钮将其删除，但是无法对其进行编辑。

![A Complete Guide to In-App Purchases for iOS Development 10](https://www.appcoda.com/wp-content/uploads/2019/10/t68_14_listed_test_users-1024x479.png)

### 在应用商店里创建一个新的应用程序

现在让我们来做一些更实在的事情，在App Store上创建一个新的应用程序记录，将其连接到真正的iOS应用程序。单击App Store主屏幕上的**我的应用(My Apps)**选项，然后，点击顶部栏左上方的**加号按钮**。

在出现的表单上有四项必须填写的内容：

1. 应用程序的名称。 必须是**独一无二的应用名称**！ 如果您提供了已经使用的名字，就会在尝试创建应用程序时会看到错误消息，只需因此进行更改即可。
2. 应用程序使用的主要语言。
3. 包ID(Bundle Id)。 使用下拉菜单找到我们先前创建的 _(应用程序ID)app ID_，该ID已连接到应用的包标识符。
4. _SKU_ –该应用程序的独一无二的字符串，在App Store中不可见。

并且在平台中选上**iOS 选项**

下图中您可以看到已完成的表格，参照这个表格完成您的填写。

![A Complete Guide to In-App Purchases for iOS Development 11](https://www.appcoda.com/wp-content/uploads/2019/10/t68_16_new_app_form.png)


如果没有缺少的必填项，同时您提供的名称是唯一的，在您单击 _创建(Create)_ 按钮后就会创建一个新应用。 页面将会自动跳转到 _app信息(App Information)_：

![A Complete Guide to In-App Purchases for iOS Development 12](https://www.appcoda.com/wp-content/uploads/2019/10/t68_17_app_info_page-1024x723.png)

### 添加应用内购

最期待的时候终于到了！ 在此部分中，我们将为我们的应用程序提供的应用内购。在此之前，让我们回忆一下我们要卖的东西：

* 一个 _消耗品(Consumable)_：游戏中三条命的应用内购
* 一个 _消耗品(Consumable)_：两个超能力点数的应用内购
* 一个 _非消耗品(Non-consumable)_：解锁全部游戏地图的应用内购

有了以上内容，让我们开始创建应用内购。单击顶部栏中的 **功能(Features)** 链接，然后在左侧菜单选项中选择 **应用内购(In-App Purchases)** 。

屏幕的主要区域是应用内购买条目的列表，现在暂时是空的。点击 **蓝色加号** 按钮添加。下面的弹出窗口将要求您选择要创建的应用内购买类型，点 **消耗品(Consumable)** ，然后点创建。

![A Complete Guide to In-App Purchases for iOS Development 13](https://www.appcoda.com/wp-content/uploads/2019/10/t68_19_select_iap_type.png)

一个新表单会出现，让我们看一下它各个部分的意思：

* **参考名称(Reference Name)**：这是在App Store中进行的应用内购买的名称，仅供内部使用，不会显示给用户，因此不必过于纠结你在这里填写的内容。但请给出一个清楚说明此应用内购内容的名称。比如，“ _额外的生命(Extra Lives)_”（不带引号）就不错，可以使我们知道这是用户可以在游戏中购买的额外生命。
* **产品ID(Product ID)**：这是用于提交的（必须是）唯一的字串（Apple说必须是字母数字的组合），我们建议：_使用App的bundle identifier作为这个ID的前缀_ 。这样一来，您就可以确保它始终是唯一的。在我们的例子中，“ _com.appcoda.fakegame.extra-lives_ ”（不带引号）是产品唯一ID。 **重要** ：请记录下您在此处创建的产品ID，稍后我们将需要使用它们。
* **待售(Cleared for Sale)**：如果你想公开应用内购，就始终选择此项。
* **定价(Pricing)**：选择您应用内购的价格。由于这只是一个演示，因此您可以选择从下拉菜单中想要的任何价格。滚动到底部可以看到替代价格。
* **应用商店信息–显示名称(App Store Information – Display Name)**：这是应用内购的名称，因为它将在应用中显示给用户。请注意：对于你应用程序每种支持的语言，您还应该提供此语言以及下面的信息的本地化版本。我在此处给第一个应用内购设置的值是“ _(获得额外的命)Get Extra Lives_”。
* **应用商店信息-描述(App Store Information – Description)**：给用户显示的应用内购的描述，也可以选择不显示。我建议你显示给用户看，这样用户获的他们将购买商品的更多详细信息。例如：“ _获得三（3）个额外的生命！_”。

填完之后是这样的：

![A Complete Guide to In-App Purchases for iOS Development 14](https://www.appcoda.com/wp-content/uploads/2019/10/t68_20_iap_completed_1-1024x446.png)

滚动到页面底部，您会看到还有两个部分：

* **App Store 促销（可选）**：默认情况下，在App Store上应用程序名称的下方会显示一句话，例如：“免费-提供应用程序内购买”。 但是，如果您想在App Store的应用程序页面上促销应用内购，可以按照Apple的提示提供促销图片。

* **评论信息(Review Information)**：在实施和测试应用内购买时不是必需的，但是在应用内购与App一起发布到App Store，或者用于TestFlight测试的 _审核时是必需的_ 。 **审核信息(Review Notes)**项不是必填，但**截图**必须有。您可以在应用显示应用内购买功能时截屏，然后上传即可。 我们暂时留空这部分，不提供这项信息，我们也可以继续。

提供完应用内购的详细信息后，请点击表单右上角的**保存(Save)**按钮。然后返回并开始创建第二个应用内购。再次选 _consumable_ IAP，并用下面的信息填写表单：

* _参考名称_：超能力点数
* _产品ID_：com.appcoda.fakegame.superpowers _（根据您的Bundle ID更改）_
* _价格_：随便填
* _显示名称_：更多超能力点数
* _说明_：额外获得两（2）点超能力点数！

![A Complete Guide to In-App Purchases for iOS Development 15](https://www.appcoda.com/wp-content/uploads/2019/10/t68_22_iap_completed_2-1024x444.png)

保存这个内购，然后创建我们最后一个内购。这次择 _非消耗品(non-consumable)_：

![A Complete Guide to In-App Purchases for iOS Development 16](https://www.appcoda.com/wp-content/uploads/2019/10/t68_23_non_consumable_iap.png)

与前两个相反，这个是每个用户只能购买一次的那种内购。但需要填写的信息相同：

* _参考名称_：解锁所有地图
* _产品ID_：com.appcoda.fakegame.unlock_maps _（根据您的Bundle ID更改）_
* _价格_：随便填
* _显示名称_：解锁地图
* _说明_：永久解锁游戏中的所有地图！

![A Complete Guide to In-App Purchases for iOS Development 17](https://www.appcoda.com/wp-content/uploads/2019/10/t68_24_iap_completed_3-1024x517.png)

我们的App将要提供的应用内购已经准备好了，您可以在IAP主页上看到所有产品：

![A Complete Guide to In-App Purchases for iOS Development 18](https://www.appcoda.com/wp-content/uploads/2019/10/t68_25_iap_listed-1024x164.png)

您会注意到，所有应用内购买的 _状态(Status)_ 都被设置成 _(缺少元数据)Missing Metadata_。 那是因为我们没有给它们设置 _审核用截图(review image)_。 不过放心，添加审核用截图后的状态将变为 _待审核(Waiting For Review)_。 这里不需要做； 我们不会发布这个演示App。

在Xcode中使用产品ID
--------------------------

终于，所有准备工作都完成了。 现在关上App Store然后打开Xcode中的Starter Project。 这篇教程的目标是创建一个可重用的类来管理应用内购，但是这个类需要知道App Store上的 _产品标识符(product identifiers)_。 因此，让我们首先将所有IAP产品标识符添加到一个特殊文件中，该文件在Xcode中的 _In-App Purchases group_ 里，叫  _IAP\_ProductIDs.plist_。

该文件的作用是让我们用简单且与代码无关的方式将产品标识符收集在同一个地方。接下来将要实现的类会通过读取此文件的内容来获取所有产品标识符。

因此，在Xcode中打开 _IAP\_ProductIDs.plist_ 并确保 _Root_ 项的**type**是 **Array**。 然后，一个一个地添加三条数据，每次复制粘贴之前创建一个应用程序内购买的产品ID。 完成后应该是这个样子：

![A Complete Guide to In-App Purchases for iOS Development 19](https://www.appcoda.com/wp-content/uploads/2019/10/t68_26_product_ids_plist.png)

实现应用内购的管理类
------------------------------------------------------
现在让我们集中精力实现这个可被重用的类，该类不仅可以在我们的Demo App 中管理应用内购买，也可以在其他地方使用。 在Xcode的starter project中，打开名为 _IAPManager.swift_ 的文件，您也可以在Project Navigator里面的 _In-App Purchases_ Group下找到这个文件。它目前是空的，但我们将在这一步和下面几步中把它完善。

第一步是导入 _StoreKit_ 框架。它可以让我们在代码层面上处理所有与应用内购相关的概念与实体。在第一个import语句之后，添加以下内容：

```swift
import StoreKit
```

声明一个新类，该类与文件同名：_IAPManager_。 留下几个空行并添加以下内容：

```swift
class  IAPManager: NSObject  {

}
```

_注意：稍后在`IAPManager`类上将采用一个名字叫`SKPaymentTransactionObserver`的协议。 这个协议要求所有符合条件的类型也要符合`NSObjectProtocol`，而我们只需将`IAPManager`设为`NSObject`类的子类就可以轻松做到这一点。_

为了让事情保持简单，避免同时有多个此类的实例所带来的麻烦，我们将采用 _单例(Singleton)_ 模式，而且是 _单一实例(one instance only)_，即 _shared instance_。 采用Singleton模式需要做两件事：

1. 将此类的实例初始化为一个静态属性。
2. 把初始化方法(initializer) 设置为 _private_，这样做的话就无法在应用程序中的任何其他位置创建该类的更多实例。

像这样:

```swift
class IAPManager: NSObject {
    static let shared = IAPManager()
 
    private override init() {
        super.init()
    }
}
```

接下来我们会看到，在某些情况下，有些操作不会返回预期的结果。 妥善处理这些情况很重要，并让`IAPManager`类在各种类似情况下正确指出错误。为此，我们要创建包含 _自定义错误(custom errors)_ 的`enum`：

```swift
enum IAPManagerError: Error {
    case noProductIDsFound
    case noProductsFound
    case paymentWasCancelled
    case productRequestFailed
}
```

请在类的主体代码内部加入这个enum，这些错误的意义分别是：

* `noProductIDsFound`：表示找不到产品标识符。
* `noProductsFound`：由于未找到任何IAP产品，因此App Store未返回。
* `paymentWasCancelled`：用户取消了已经开始了的购买过程。
* `productRequestFailed`：由于某种原因，App无法向App Store请求可用的IAP产品。

除了上面的Enum，还要紧接着在`IAPManager`类主体代码之后添加下面这个扩展(extension)。在里面定义我们自定义错误的 _本地化说明(localized descriptions)_:

```swift
extension IAPManager.IAPManagerError: LocalizedError {
    var errorDescription: String? {
        switch self {
        case .noProductIDsFound: return "No In-App Purchase product identifiers were found."
        case .noProductsFound: return "No In-App Purchases were found."
        case .productRequestFailed: return "Unable to fetch available In-App Purchase products at the moment."
        case .paymentWasCancelled: return "In-App Purchase process was cancelled."
        }
    }
}
```

读取产品标识符(Product Identifiers)
---------------------------

在执行与应用内购相关的任何操作之前，要拿到我们先前添加到 _IAP\_ProductIDs.plist_ 文件中的产品标识符。 为此，我们将实现一个小的辅助方法完成该任务：从 _应用程序包(app bundle)_ 中读取属性列表文件，并以 _字符串数组_ 的形式返回产品标识符。

```swift
fileprivate func getProductIDs() -> [String]? {
 
}
```

该方法被标记为`fileprivate`，因为我们想让它仅在此文件中可见。没理由向除了这个类意外的任何其他实体开放访问。但是，如果您需要在此处以外的地方使用产品标识符，可以删除`fileprivate`关键字。

此方法的第一步是获取指向应用程序包中 _IAP\_ProductIDs.plist_ 文件的 _URL_：

```swift
guard let url = Bundle.main.url(forResource: "IAP_ProductIDs", withExtension: "plist") else { return nil }
```

使用上述方法时，务必要正确输入文件名及扩展名；打错字会让你奇怪为什么即使项目中存在该文件也找不到该文件并且浪费时间。如果找到文件，则将其URL分配给`url`属性。

接下来，我们将文件内容加载到 _Data_ 的NSObject：

```swift
do {
    let data = try Data(contentsOf: url)
} catch {
    print(error.localizedDescription)
    return nil
}
```

像上面那样初始化Data对象可能会引起异常，因此需要将其包含在`do-catch`语句中。如果文件无法读取但要初始化`data`属性，那么我们就可以将打印出错误的描述，然后返回`nil`。

由于上述`data`对象是通过使用 _property list file_ 的内容创建出的，因此我们要在[_PropertyListSerialization_](https://developer.apple.com/documentation/foundation/propertylistserialization)类的帮助下将其转换为String数组；它允许对属性列表对象进行编码和解码。如果您熟悉 _JSONSerialization_ 类，这两个类基本等效，只是一个作用在Property list，一个作用于JSON对象。

在do-catch语句中，让我们把加载的属性列表数据转换为数组，如下：

```swift
let productIDs = try PropertyListSerialization.propertyList(from: data, options: .mutableContainersAndLeaves, format: nil) as? [String] ?? []
```

在无法转换成字符串数组的情况下，我们就赋一个空数组给`productIDs`。

最后我们需要返回结果:

```swift
return productIDs
```

这是完整的方法：

```swift
fileprivate func getProductIDs() -> [String]? {
    guard let url = Bundle.main.url(forResource: "IAP_ProductIDs", withExtension: "plist") else { return nil }
    do {
        let data = try Data(contentsOf: url)
        let productIDs = try PropertyListSerialization.propertyList(from: data, options: .mutableContainersAndLeaves, format: nil) as? [String] ?? []
        return productIDs
    } catch {
        print(error.localizedDescription)
        return nil
    }
}
```

向App Store请求可用的IAP商品
-----------------------------------------------

要让`IAPManager`能够加载产品ID，下一步就是从App Store读取所有可用的产品。此操作将返回`SKProduct`对象的数组，其中每个对象都描述了对应的应用内购和其在App Store中配置的详细信息。

我们先定义一个新方法：

```swift
func getProducts(withHandler productsReceiveHandler: @escaping (_ result: Result<[SKProduct], IAPManagerError>) -> Void) {
 
}
```

先谈谈这个方法的参数，让我先问你一个问题：你是否了解在Swift 5中新加入的 [**Result**](https://developer.apple.com/documentation/swift/result)？这是官方文档的介绍：

_代表成功或失败的值，每种情况下都有一个相应的关联值。(A value that represents either a success or a failure, including an associated value in each case.)_

简而言之，使用这种类型可以很容易地返回操作结果并说明操作是否成功。成功后，可以携带任何必要的自定义数据；失败时，会携带导致操作失败的错误。

在我们的例子中，如果成功，我们的`Result`值将会含有 _从App Store中获取的商品的集合_，如果失败，将会含有我们的自定义错误类型(`IAPManagerError`)。

从App Store提取IAP产品是一个异步过程。 这意味着上述方法 _无法立即返回获取的产品_。 因此，该方法的参数必须是 _closure_（或 _callback handler_），当`StoreKit`通知我们的类它已经收到App Store的响应时，该方法将被调用。如上方法定义所示，该闭包的参数是`Result`值。

The above borns a new requirement now: To declare a property in the `IAPManager` class which will keep a reference to the handler (closure) even when the execution of the `getProducts(withHandler:)` method is finished. Go to the beginning of the class in the properties declaration area and add the following:

根据上面的说法，我们有了一个新需求：在`IAPManager`声明一个属性用来在`getProducts(withHandler:)`方法调用完成之后继续保留对其handler（closure）的引用。到开头部分的属性声明区域添加如下代码：

```swift
var onReceiveProductsHandler: ((Result<[SKProduct], IAPManagerError>) -> Void)?
```

现在回到`getProducts(withHandler:)`方法。我们开始实现这个方法，首先是把`productsReceiveHandler`赋给`onReceiveProductsHandler`以便在以后的任何时间使用它。

```swift
onReceiveProductsHandler = productsReceiveHandler
```

接下来，让我们获取要为其请求具体数据的产品的标识符。为了实现这一目的， 我们将用到我们之前已经实现了的`getProductIDs()`方法。

```swift
guard let productIDs = getProductIDs() else {
    productsReceiveHandler(.failure(.noProductIDsFound))
    return
}
```

记住，上面的逻辑可能会返回`nil`。 如果发生这种情况，则是由于某种原因无法读取所需的产品标识符，我们应立即从此方法返回。但是，有必要将发生的错误通知给类的调用者。 我们调用`productsReceiveHandler`，然后传过去带有 _failure_ 的`Result`类型，来说明发生了什么错误。

_注意：如果您对使用`Result`类型的语法感到不太习惯，那么我建议您在网上搜索一些使用示例，并在Playground中试着用用，直到掌握为止。_

继续往下写，让我们初始化一个向App Store的 _products request_：

```swift
let request = SKProductsRequest(productIdentifiers: Set(productIDs))
```

上面显示的初始化方法正在等待产品标识符的`Set`，而不是数组。所以我们使用`productIDs`数组初始化来建立新的`Set`。

如前所述，请求App Store的操作不是同步的。它的结果（来自App Store返回的响应）可通过`SKProductsRequestDelegate`和`SKRequestDelegate`协议所提供的几种方法获得。 `IAPManager`类将采用这些协议，但首先必须将其设置为request的delegate：

```swift
request.delegate = self
```

我们现在就可以发发出请求了：

```swift
request.start()
```

`getProducts（withHandler :)`这个方法虽然很小但是很重要，但它里面有使应用内购买成为可能的重要步骤。下面是整个方法：

```swift
func getProducts(withHandler productsReceiveHandler: @escaping (_ result: Result<[SKProduct], IAPManagerError>) -> Void) {
    // Keep the handler (closure) that will be called when requesting for
    // products on the App Store is finished.
    onReceiveProductsHandler = productsReceiveHandler
 
    // Get the product identifiers.
    guard let productIDs = getProductIDs() else {
        productsReceiveHandler(.failure(.noProductIDsFound))
        return
    }
 
    // Initialize a product request.
    let request = SKProductsRequest(productIdentifiers: Set(productIDs))
 
    // Set self as the its delegate.
    request.delegate = self
 
    // Make the request.
    request.start()
}
```

处理App Store的响应
---------------------------

上面，我们已经将`IAPManager`类设置为`SKProductsRequest`对象(request)的委托。现在，必须采用`SKProductsRequestDelegate`协议并至少写出一种必需的方法。

在`IAPManager`类的花括号后面，添加下面的扩展：

```swift
extension IAPManager: SKProductsRequestDelegate {
 
}
```

在里面我们将实现下面的方法，这个方法会在App Store响应我们发出的请求时被调用：

```swift
func productsRequest(_ request: SKProductsRequest, didReceive response: SKProductsResponse) {
 
}
```

The first parameter value regards the request that triggered the response. The second (the `response`) is what we really care about here, as it contains a collection of `SKProduct` objects; the available content for purchase. Each product contained in the response matches to a single product identifier in the list of identifiers existing in our property list file.

第一个参数是触发响应的请求。 第二个参数(`request`)是我们在这里真正关心的，因为它包含`SKProduct`对象的集合：可购买内容。
响应中包含的每个产品都与我们属性列表文件中存在的标识符列表中的单个产品标识符匹配。

Going into the logic that we’ll apply here, at first we’ll get the collection of products:

```swift
let products = response.products
```

Then we’ll check if there are any products returned or not. If so, we’ll call the `onReceiveProductsHandler` and we’ll pass a `Result` value indicating a success and supplying it with the array of products:

```swift
if products.count > 0 {
    onReceiveProductsHandler?(.success(products))
} else {
 
}
```

Among others, the above illustrates how a `Result` value with the case of success is being constructed.

In case there are no products returned, we’ll call the `onReceiveProductsHandler` once again, but this time the `Result` value will indicate an error, and we’ll pass the `noProductsFound` custom error as its associated value:

```swift
onReceiveProductsHandler?(.failure(.noProductsFound))
```

Even though finding no products is not necessarily an error (when, for example, there are no IAP entries on App Store or the given product identifiers do not match to any IAPs), treating it the way shown above ensures that the two cases of having and not having products will be handled separately by the caller of this class.

Here’s the entire method:

```swift
func productsRequest(_ request: SKProductsRequest, didReceive response: SKProductsResponse) {
    // Get the available products contained in the response.
    let products = response.products
 
    // Check if there are any products available.
    if products.count > 0 {
        // Call the following handler passing the received products.
        onReceiveProductsHandler?(.success(products))
    } else {
        // No products were found.
        onReceiveProductsHandler?(.failure(.noProductsFound))
    }
}
```

_Note: The `response` object provides a property called `invalidProductIdentifiers`. It’s a collection of identifiers regarding products that are not valid to be purchased. Although we do not use it here, keep it in mind in case you ever need it._

What the above method does not do is to let us know if the request failed for some reason. We can deal with that by implementing another delegate method:

```swift
func request(_ request: SKRequest, didFailWithError error: Error) {
 
}
```

Regardless of the error that caused the request to fail, it’s necessary to let the `IAPManager` caller know about it. For one more time we’re going to call the `onReceiveProductsHandler` closure passing the `productRequestFailed` custom error.

```swift
onReceiveProductsHandler?(.failure(.productRequestFailed))
```

Finally, there’s one more delegate method which can be optionally implemented:

```swift
func requestDidFinish(_ request: SKRequest) {
 
}
```

We won’t use it here, but be aware of it in case there’s any additional custom logic you want to add.

Getting Product’s Price As A String
-----------------------------------

In the next part we’re going to have a first taste about everything we’ve done so far, but before that it’s necessary to implement a small assistive, yet necessary method in the `IAPManager` class. Its purpose is to _return a product’s price as a formatted currency string_. The implementation shown right next is taken as-is from Apple’s documentation but it’s not hard to understand it:

```swift
func getPriceFormatted(for product: SKProduct) -> String? {
    let formatter = NumberFormatter()
    formatter.numberStyle = .currency
    formatter.locale = product.priceLocale
    return formatter.string(from: product.price)
}
```

Notice that the string is created based on the product’s localized price value which is taken by accessing the `priceLocale` property.

Testing What Is Built So Far
----------------------------

Let’s take a small break from building the `IAPManager` class now, and let’s make a few updates to other parts of the app so we can run it and test what we’ve done so far. There are two things that we’ll do here:

1.  We’ll fetch the available in-app purchases from the App Store (the products).
2.  We’ll display product information while trying to initiate a buy process.

_Note: Since the goal of this post is to focus on the creation of a reusable component to perform in-app purchases, I’ll save us some time by being less thorough when presenting missing parts of the app which are not related directly to the `IAPManager` class. Please take your time and have a good look at the project if you need so._

### Fetching IAP Products

Before users are able to make any in-app purchase, the app must fetch all information about available IAPs from the App Store. There’s no rule when exactly apps should do that, it always depends on each specific app itself. Sometimes it’s suitable to fetch products right before an in-app purchase is started. Some other times it’s better to fetch them right after the app has launched.

In this demo application, and given the fact that we have one view controller only, we’ll fetch the available IAP products right after the view of the view controller has appeared and it’s ready to be used. If you check the `viewDidAppear(_:)` method in the _ViewController.swift_ file, you’ll see that it calls the `viewDidSetup()` method of the `ViewModel` instance; that is place where we’ll initiate the product fetching.

Open the _ViewModel.swift_ file and locate the `viewDidSetup()` method. When there are no products fetched recently by the App Store, no in-app purchase process can start, so users have to wait a bit. Let’s do that, and let’s trigger the appearance of _an overlay view with an activity indicator in it_ while the app “talks” to the App Store and requests for any in-app purchases:

```swift
func viewDidSetup() {
    delegate?.willStartLongProcess()
}
```

Now we can use the `getProducts(withHandler:)` we implemented earlier in the `IAPManager` class. Remember that `IAPManager` is a _singleton_ class therefore we access the method through its `shared` instance:

```swift
IAPManager.shared.getProducts { (result) in
 
}
```

We can remove the overlay view shown in the app when the fetching process is finished and the above closure gets called. Just remember to do that on the _main thread_.

```swift
DispatchQueue.main.async {
    self.delegate?.didFinishLongProcess()
}
```

Let’s handle the `result` now. If it’s a success, then it’ll contain the fetched IAP products; if it’s a failure it’ll carry the error that caused it. In the first case we keep the fetched products in the `products` property of our `model`; in the second we trigger the appearance of an alert that will display the _localized description_ of the returned error:

```swift
switch result {
    case .success(let products): self.model.products = products
    case .failure(let error): self.delegate?.showIAPRelatedError(error)
}
```

Find the implementation of the `showIAPRelatedError(_:)` delegate method in the _ViewController.swift_ file to find out how the error is being displayed.

Run the app now if you want (either in Simulator or on a device) and look the overlay view with the spinner appearing while the app is fetching the in-app purchase information from the App Store. If it just disappears after a few moments, then products have been successfully fetched and kept by the app. If any error occurs, you’ll see an alert showing up with the error description.

Here’s the entire method we just implemented:

```swift
func viewDidSetup() {
    delegate?.willStartLongProcess()
 
    IAPManager.shared.getProducts { (result) in
 
        DispatchQueue.main.async {
            self.delegate?.didFinishLongProcess()
 
            switch result {
            case .success(let products): self.model.products = products;
            case .failure(let error): self.delegate?.showIAPRelatedError(error)
            }
        }
    }
}
```

Note: The way you’ll temporarily keep fetched IAP products in your app is totally up to you. Here I chose to create the `products` property in the `Model` class, which is an array of `SKProduct` elements.

### Displaying Product Information

By having the available in-app purchase products at the app’s disposal, we can use them for presenting certain information to users when a purchase process is about to start. To be specific, we’ll be displaying the following for each product a user wants to purchase:

*   The localized title.
*   The localized description.
*   The price.

Remember that all of them have been set on the App Store. And here’s something to avoid: _Do not hardcode any title, description or price into the app; all these can change at any time if you edit your in-app purchase records on the App Store_.

Let’s focus on our demo app again now. We will initiate a purchase process when a user taps on a cell and any of the following is true:

*   The first cell is tapped and there are no extra lives available.
*   The second cell is tapped and there are no super powers available.
*   The third cell is tapped (Note: Third cell won’t be visible after unlocking all maps by buying the matching product; it regards a _non-consumable_ product).

This logic is already applied in the `tableView(_:didSelectRowAt:)` table view delegate method in the _ViewController.swift_ file. Go there and take a look now. When any of the above conditions is true, the `getProductForItem(at:)` method of the `ViewModel` is called in order to return the matching product to the tapped cell. That product is given as an argument to the `showAlert(for:)` method defined in the `ViewController` class.

The purpose of the `showAlert(for:)` method is to display an alert controller which will contain all the product details mentioned above right before an in-app purchase is made. It actually displays an alert that asks users if they want to continue with the purchase of the described item.

_Note: I would recommend against of using a default alert controller for displaying a product’s information, unless your app is implements a really basic UI. You’d better describe products in custom designed views that match to the look and feel of your app._

Locate the `showAlert(for:)` method in the _ViewController.swift_ file. We’ll start implementing it by getting the _localized price_ of the product given as an argument:

```swift
func showAlert(for product: SKProduct) {
    guard let price = IAPManager.shared.getPriceFormatted(for: product) else { return }
}
```

Let’s initialize an alert controller now. We’ll display the product’s title as the alert’s title, and the product’s description as the alert’s message:

```swift
let alertController = UIAlertController(title: product.localizedTitle,
                                        message: product.localizedDescription,
                                        preferredStyle: .alert)
```

The product’s price will be used as part of the title of an alert action that will prompt users to buy the item. Here it is:

```swift
alertController.addAction(UIAlertAction(title: "Buy now for \(price)", style: .default, handler: { (_) in
    // TODO: Initiate Purchase!
}))
```

The button’s title will say something: “Buy now for $0.49”. When users tap on it, the actual purchase process will be initiated, but that’s for later. For now we just leave a “TODO” comment.

Finally, let’s add a _cancel_ action to the alert, and let’s present it:

```swift
alertController.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))
self.present(alertController, animated: true, completion: nil)
```

Here’s the entire method:

```swift
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
```

Run the app now and tap on any cell. You should see the alert we just created is showing up and describing the selected product! Of course no purchase can take place now, and that’s something that we’re going to change right now!

![A Complete Guide to In-App Purchases for iOS Development 20](https://www.appcoda.com/wp-content/uploads/2019/10/t68_27_product_description-571x1024.png)

Implementing Purchases
----------------------

In programming level, a purchase is a transaction (aka a `SKPaymentTransaction` object), and responsible for managing transactions is a class called _SKPaymentQueue_. The payment queue is the one which communicates with the App Store and handles all the payment process. It’s also responsible for presenting the built-in UI that appears when performing purchases.

Every app that wants to offer in-app purchases is required _to add a payment transaction observer to the queue (a `SKPaymentTransactionObserver` object)_ so it’s capable of catching any updates regarding a purchase. As you understand, making purchases is an asynchronous process just like fetching the products information from the App Store. The outcome of a transaction should always become absolutely clear to users, regardless of whether it was successful or not.

### Observing The Payment Queue

So, with that new information in mind, let’s go back to the _IAPManager.swift_ file where we’ll implement two new small methods. With them we’ll be adding and removing a payment transaction observer to the payment queue respectively:

```swift
func startObserving() {
    SKPaymentQueue.default().add(self)
}
 
 
func stopObserving() {
    SKPaymentQueue.default().remove(self)
}
```

Just implementing the above two is not enough. It’s also necessary to call them appropriately so the app can start and stop observing for payment transaction updates as needed.

Open the _AppDelegate.swift_ file. In the `application(_:didFinishLaunchingWithOptions:)` method make call to `startObserving()` so the app starts observing each time is launched:

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    IAPManager.shared.startObserving()
    return true
}
```

Next, implement the `applicationWillTerminate(_:)` to stop observing:

```swift
func applicationWillTerminate(_ application: UIApplication) {
    IAPManager.shared.stopObserving()
}
```

That’s it. The app is now capable of watching properly for updates in the payment queue, and we are ready to move forward and make it possible to create new transactions (meaning to make new purchases), as well as to handle any transaction related updates coming from the App Store.

_Note: Don’t be bothered by the couple of errors currently shown by Xcode; they’ll be fixed soon._

### Checking For IAP Eligible Device

Before an app triggers a purchase process, it’s important to know whether in-app purchases are actually _allowed or supported_ on a device. It’s possible, for example, that parents have turned off IAPs through the device settings. That’s a state that apps must always check for and notify users accordingly.

The information of whether in-app purchases can be made is something that’s taken from the payment queue once again. Go to the _IAPManager.swift_ file and implement the following method:

```swift
func canMakePayments() -> Bool {
    return SKPaymentQueue.canMakePayments()
}
```

As you see, it’s as simple as that. We will call `canMakePayments()` method later on, when we’ll keep adding the missing parts from the demo app that will make purchases possible.

### Making Purchases

With the payment queue being observed and with the ability to know whether a device can actually make payments or not, let’s go to the implementation of a new method that triggers a new purchase:

```swift
func buy(product: SKProduct, withHandler handler: @escaping ((_ result: Result<Bool, Error>) -> Void)) {
    let payment = SKPayment(product: product)
    SKPaymentQueue.default().add(payment)
 
    // Keep the completion handler.
    onBuyProductHandler = handler
}
```

This method accepts two arguments:

1.  The first one is the actual product (a `SKProduct` object) that is about to be purchased.
2.  The second parameter is a callback handler ( a closure) similar to the one we met in the `getProducts(withHandler:)` method. It has a `Result` type as a parameter with a boolean value as the associated value for the success case, and an error object as the associated value for the failure case. This callback handler is necessary because making a payment is an asynchronous operation; it’ll be called when the payment process is finished.

Inside the method now, we initialize a payment object (`SKPayment`) using the given product. Then, we add the payment object to the payment queue and we’re done! Once a new payment object is added to the payment queue, things will start working without any other action on our behalf, so we just have to wait for the entire process to come to its end.

The callback handler that is given as an argument to this method should be kept in a class property, so it’s available when the method’s execution is finished. This is what’s happening in the last line above; the `handler` parameter value is assigned to the `onBuyProductHandler`. However, the second one does not exist in the `IAPManager` class yet.

Go to the properties declaration area of the class and add the following line:

```swift
var onBuyProductHandler: ((Result<Bool, Error>) -> Void)?
```

### Handling Transaction Results

Now that we made it possible to make a payment, let’s handle the results of a transaction. For this purpose, we need to adopt the `SKPaymentTransactionObserver` protocol and implement a required method.

Go after the closing curly bracket of the `IAPManager` class once again, and add one more extension:

```swift
extension IAPManager: SKPaymentTransactionObserver {
 
}
```

In it we’ll implement the following method:

```swift
extension IAPManager: SKPaymentTransactionObserver {
    func paymentQueue(_ queue: SKPaymentQueue, updatedTransactions transactions: [SKPaymentTransaction]) {
 
    }
}
```

This method is called when the state of a transaction changes. The second parameter value, `transactions`, is an array of `SKPaymentTransaction` objects as multiple transactions can be running at the same time. We’ll start with that and we’ll go through each transaction:

```swift
transactions.forEach { (transaction) in
 
}
```

Next, we’ll add a `switch` statement which we’ll use to examine the various states of each transaction:

```swift
switch transaction.transactionState {
case .purchased:
 
case .restored:
 
case .failed:
 
case .deferred, .purchasing: break
@unknown default: break
}
```

What we are really interested about is the first three cases:

1.  `purchased`: When a payment is finished and the product has been purchased.
2.  `restored`: When a previously purchased item has been restored from the App Store.
3.  `failed`: When a transaction fails.

In the first case where a purchase is successful, we’ll call the `onBuyProductHandler` closure passing `true` as the value of the result object. This will notify the `IAPManager`‘s caller about the successful outcome. On top of that, there’s one more action that’s mandatory to be done; to indicate to the payment queue that it can now consider the transaction as finished.

```swift
case .purchased:
    onBuyProductHandler?(.success(true))
    SKPaymentQueue.default().finishTransaction(transaction)
```

Actually, the last line is something we’ll also do in the next cases too.

We’ll postpone the `restored` case for a bit later. For now add this:

```swift
case .restored: break
```

In the `failed` case, we’ll check at first if the transaction’s `error` object is `nil` or not. A transaction’s error is a `SKError` object and it has a specific code that describes the error. There’s a special one called `paymentCancelled` and it’s happening when a user cancels a purchase right before the actual payment is done. If this is the case, then we’ll pass a failure value to the result with the custom `IAPManagerError.paymentWasCancelled` error. If not, then we’ll pass failure with the actual error that took place. That way we’ll make it easy for the app to know whether the payment failed because of the user or not.

case .failed: if let error = transaction.error as? SKError { if error.code != .paymentCancelled { onBuyProductHandler?(.failure(error)) } else { onBuyProductHandler?(.failure(IAPManagerError.paymentWasCancelled)) } print("IAP Error:", error.localizedDescription) } SKPaymentQueue.default().finishTransaction(transaction)

```swift
case .failed:
if let error = transaction.error as? SKError {
    if error.code != .paymentCancelled {
        onBuyProductHandler?(.failure(error))
    } else {
        onBuyProductHandler?(.failure(IAPManagerError.paymentWasCancelled))
    }
    print("IAP Error:", error.localizedDescription)
}
SKPaymentQueue.default().finishTransaction(transaction)
```

Implementing the capability to make in-app purchases is now complete. Time to try it out!

Enable App To Make Purhases
---------------------------

Open the _ViewModel.swift_ file where you’ll find a defined, yet still empty method called `purchase(product:)`. In this one we’ll add the missing logic which will make use of what we just did in the previous parts and ultimately make payments come true for our demo app.

As you can see the returned value from the method is a boolean one. The only case where this method will return `false` is going to be when no payments can be made on a specific device. Otherwise it’ll trigger the new payment using the product provided as an argument and it’ll return `true`. Let’s see that:

```swift
func purchase(product: SKProduct) -> Bool {
    if !IAPManager.shared.canMakePayments() {
        return false
    } else {

    }

    return true
}
```

Here’s the place where we make use of the `canMakePayments()` method which we implemented earlier.

Focusing on the `else` case now, we’ll indicate to the View part of our MVVM based app that a long process is about to start. This will make the overlay view with the activity indicator appear:

```swift
delegate?.willStartLongProcess()
```

Let’s trigger the purchase:

```swift
IAPManager.shared.buy(product: product) { (result) in
 
}
```

We’re calling the `buy(product:)` method of the `IAPManager` class passing the `product` parameter value given to the `purchase(product:)` as its argument. The above closure is the one assigned to the `onBuyProductHandler` property in the `IAPManager` class. It’ll be called back when the purchase will have either finished successfully or not; it’s the `result` value to tell that.

Since the purchase is an asynchronous operation taking place on the background, whatever we’ll do in the above closure must be done on the main thread. After all, we’re going to initiate UI changes and that’s something that cannot be done using a background thread.

```swift
DispatchQueue.main.async {
    self.delegate?.didFinishLongProcess()
 
    switch result {
    case .success(_): self.updateGameDataWithPurchasedProduct(product)
    case .failure(let error): self.delegate?.showIAPRelatedError(error)
    }
}
```

By calling the `didFinishLongProcess()` delegate method we make the overlay view disappear. Next, we examine the `result` value. On success we call the `updateGameDataWithPurchasedProduct(_:)` method which is already implemented in the `ViewModel` class. This is the method which, depending on the purchased product, will add extra lives or extra powers to our fake game (consumable products), or it will mark all maps as unlocked (non-consumable). Go and take a look at `updateGameDataWithPurchasedProduct(_:)`.

If the purchase fails, then we let our view controller know about that and therefore show the error message through the `showIAPRelatedError(_:)` delegate method. Note that the way a purchase’s results will be handled is totally depending on the app that’s being developed, the logic applied to it, the design, and so on. So, what you see above is something that has a meaning to this demo app only. However, this doesn’t mean you cannot act similarly in your own apps; feel free to use this method as a guide.

Here’s the `purchase(product:)` all together:

```swift
func purchase(product: SKProduct) -> Bool {
    if !IAPManager.shared.canMakePayments() {
        return false
    } else {
        delegate?.willStartLongProcess()
        IAPManager.shared.buy(product: product) { (result) in
            DispatchQueue.main.async {
                self.delegate?.didFinishLongProcess()

                switch result {
                case .success(_): self.updateGameDataWithPurchasedProduct(product)
                case .failure(let error): self.delegate?.showIAPRelatedError(error)
                }
            }
        }
        return true
    }
}
```

Open the _ViewController.swift_ file now and search for this comment: _// TODO: Initiate Purchase!_. You should find it in the action of the alert controller that displays the product information that is about to be purchased (`showAlert(for:)` method). Replace that comment and update the action as shown next:

```swift
alertController.addAction(UIAlertAction(title: "Buy now for \(price)", style: .default, handler: { (_) in
 
    if !self.viewModel.purchase(product: product) {
        self.showSingleAlert(withMessage: "In-App Purchases are not allowed in this device.")
    }
 
}))
```

If the `purchase(product:)` returns `false`, then the device cannot make payments and we’re showing an alert to let users know about that. Otherwise, things will flow as we built it earlier.

Testing In-App Purchases
------------------------

In order to test in-app purchases _you’ll need a real device_. Payments cannot be done using a Simulator. However **before you begin** make sure to **sign out from your iCloud account** if you’re already connected in your device. We want all tests to take place on a sandboxed environment.

After you sign out, _do not connect to iCloud using a test account_. This is not the place to do it! You’ll be asked to sign in when you initiate a purchase process. However, in case you want to set the test account you’ll use in advance, or if you want to change the test user later, then:

*   Open Settings.
*   Go to **iTunes & App Store**.
*   Scroll to bottom. There’s a section called **Sandbox Account**. Tap on _Sign-In_ and provide the test user credentials.

_Note: Do not provide a real Apple ID here!_

In addition to the above, you might want to disallow in-app purchases in your device so you can test that case too. In order to do that, go to Settings and then:

*   _Screen Time_
*   _Content & Privacy Restrictions_
*   _iTunes & App Store Purchases_
*   _In-app Purchases_
*   Select _Don’t Allow_

To enable IAPs again, follow the same steps but at the end select _Allow_.

Now that you know how to assign or change a test user, and how to turn on and off in-app purchases, compile the app and let it run on your device. Tap on any fake product you want to buy. For example, tap on the extra lives cell and then on the _Buy_ button. Provide the test user’s credentials and confirm the payment. Once it’s successfully done, you’ll see that you have three available lives which you can _consume_ by tapping on the cell. When you’re out of lives, you’ll be asked to buy again.

_Note: Pay attention to the system alerts that are showing up. While confirming the purchase you should be seeing the **\[Environment: Sandbox\]** indication. If you don’t see that, then you’re not making the purchase using a test account and you should not proceed until you change that._

![A Complete Guide to In-App Purchases for iOS Development 21](https://www.appcoda.com/wp-content/uploads/2019/10/t68_3_purchase_process-1-1024x577.png)

In case you start the purchase but you cancel it before confirming the buy, here’s what you’ll see:

![A Complete Guide to In-App Purchases for iOS Development 22](https://www.appcoda.com/wp-content/uploads/2019/10/t68_29_cancelled_payment-576x1024.png)

Note that you can keep buying extra lives and super powers for as long as you want when running out of them. However, unlocking all maps can be purchased just once; it’s a non-consumable product, and when you do so the last cell will no longer be appearing on the table view.

Restoring Purchases
-------------------

Even though we made it possible to perform in-app purchases and buy digital products through our app, there’s still one thing missing. That is the option to _restore previously purchased non-consumable products_.

When users pay once for a non-consumable product (like unlocking all maps in our demo app), they should never be asked to pay for it again. However, they should be able to get their purchase back when they install the app on a different device or when they re-install on the same one. Apple _requires_ a restore button to exist in our UI which should be clearly visible and understandable by users. Without the option to restore, get ready to see your app being rejected from the App Store.

Thankfully, App Store keeps record of purchases made on non-consumable products and it will prevent users from paying again for something they’ve already bought. That means that even if someone tries to buy again a non-consumable product will fail; Once `StoreKit` and the payment queue get the information from the App Store that there is a previous payment, the new process will be considered as a restore and the user won’t be charged for it.

Restoring purchases is something we’re still missing in the `IAPManager` class, so let’s take care of it now. Open the _IAPManager.swift_ file and add the following new method:

```swift
func restorePurchases(withHandler handler: @escaping ((_ result: Result<Bool, Error>) -> Void)) {
    onBuyProductHandler = handler
    totalRestoredPurchases = 0
    SKPaymentQueue.default().restoreCompletedTransactions()
}
```

Let’s start with the parameter value. Once again it’s the (familiar) callback handler we also had in the `buy(product:)` method too, so we assign it to the `onBuyProductHandler` class property for future use.

You might be wondering what the `totalRestoredPurchases` variable is all about. When having restorable products, it’s good to know whether all of them were restored or not, and by using such a _counter_ you can easily verify it. You’ll see how it’ll be used in a while, but first, declare it in the `IAPManager` class along with the other class properties:

```swift
var totalRestoredPurchases = 0
```

Finally, by calling the `restoreCompletedTransactions()` method of the payment queue we’re triggering the process of restoring previous purchases.

_Note: Restoring previous purchases regards only non-consumable products. Consumable products are not restored; users might have or might have not consumed them and it’s up to you as a developer to synchronize that information among devices if necessary using cloud solutions (such as iCloud, your server, Firebase, etc)._

Now, go to the `paymentQueue(_:updatedTransactions:)` delegate method in the `SKPaymentTransactionObserver` extension. If you remember, there’s the `restored` case there which we had left unmanaged. Since in this method we go through all available transactions, we’ll increase the `totalRestoredPurchases` value by one each time we have a restored one. At the end we’ll know how many purchases were restored and how many weren’t. Here’s how the `restored` case should look:

```swift
case .restored:
    totalRestoredPurchases += 1
    SKPaymentQueue.default().finishTransaction(transaction)
```

Notice that calling the `finishTransaction(_:)` method is necessary so the transaction is marked as finished in the payment queue.

We’ll implement two more delegate methods now in the `SKPaymentTransactionObserver` extension’s body. The one you’ll see right next is called whenever restoring previous purchases has finished successfully, meaning without any errors, _even if there are not any products to restore_.

```swift
func paymentQueueRestoreCompletedTransactionsFinished(_ queue: SKPaymentQueue) {
    if totalRestoredPurchases != 0 {
        onBuyProductHandler?(.success(true))
    } else {
        print("IAP: No purchases to restore!")
        onBuyProductHandler?(.success(false))
    }
}
```

Two things to mention here: Firstly, see that in both cases we call the `onBuyProductHandler` passing `success` as the result’s value. The associated value of the success cases varies depending on whether we had restored products or not. Secondly, the way `totalRestoredPurchases` property is used here is something you can change and make it fit to the needs of your own app. In this demo app it’s sufficient to know that `totalRestoredPurchases != 0`, since we have one non-consumable product only and this condition ensures that it’s restored successfully. If you have more than one restorable products, you might need to change it and calculate the products that were not restored.

The second delegate method is invoked by the payment queue on a failed restore process. Note that a reason for the failure could be users themselves by just cancelling the entire process. You’ll find it familiar as we’re doing the exact same thing as we did in the `failed` case in the `paymentQueue(_:updatedTransactions:)` method. Here it is:

```swift
func paymentQueue(_ queue: SKPaymentQueue, restoreCompletedTransactionsFailedWithError error: Error) {
    if let error = error as? SKError {
        if error.code != .paymentCancelled {
            print("IAP Restore Error:", error.localizedDescription)
            onBuyProductHandler?(.failure(error))
        } else {
            onBuyProductHandler?(.failure(IAPManagerError.paymentWasCancelled))
        }
    }
}
```

And that concludes the implementation of the `IAPManager` class! It’s about time to run our last test.

Enabling App To Restore Purchases
---------------------------------

Open the _ViewModel.swift_ file and go to the `restorePurchases()` method. This one is called by the `restorePurchases(_:)` IBAction method in the `ViewController` class. Similarly to what we did before, we’ll indicate a long process to the view controller, and then we’ll initiate the restore process:

```swift
func restorePurchases() {
    delegate?.willStartLongProcess()
    IAPManager.shared.restorePurchases { (result) in
 
    }
}
```

When the closure gets called, we’ll examine the success value of the result. If it’s true, then restoring the product was successful and we can unlock all maps on our fake game. If not we’ll display an appropriate message, which is also the case when restoring fails.

```swift
DispatchQueue.main.async {
    self.delegate?.didFinishLongProcess()
 
    switch result {
    case .success(let success):
        if success {
            self.restoreUnlockedMaps()
            self.delegate?.didFinishRestoringPurchasedProducts()
        } else {
            self.delegate?.didFinishRestoringPurchasesWithZeroProducts()
        }
 
    case .failure(let error): self.delegate?.showIAPRelatedError(error)
    }
}
```

See that we treat separately the case where there are no products to restore (the `didFinishRestoringPurchasesWithZeroProducts()` delegate method is called). You’re not required, but recommended to do so in your apps as well so you can notify your users properly.

In order to test the restore functionality, first buy the “unlock all maps” in-app purchase and then delete the app from your device. Install it again through Xcode and then tap on the _Restore Purchases_ button using _the same test user_ you used before.

![in-app-purchase-cancel](https://www.appcoda.com/wp-content/uploads/2019/10/t68_30_restore_iaps-576x1024.png)

Summary
-------

Offering in-app purchases through an app is not a complicated process, but it involves a lot of steps. Starting with the preparation on the App Store and finishing by adding the last touch in your code consists of a series of actions that must be done really carefully. The detailed parts presented in this post show the way to integrate in-app purchases in your app, and by leaving here you can keep the `IAPManager` class as a reusable component which you can extend according to your needs. Always make sure to be in align with the Apple’s recommendations and best practices regarding in-app purchases. With that said it’s about time to let you rest because you’ve just gone through a long tutorial, which I hope you found useful. Thanks for reading and… happy earnings!

For the full source code, you can refer to [this project](https://github.com/appcoda/In-app-Purchase-Game-Demo/) on GitHub.

_Credits: Images by [icons8](https://icons8.com)_
