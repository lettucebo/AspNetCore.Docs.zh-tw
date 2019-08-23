---
title: 處理 ASP.NET Core Blazor 應用程式中的錯誤
author: guardrex
description: 探索 ASP.NET Core 如何 Blazor Blazor 如何管理未處理的例外狀況, 以及如何開發偵測和處理錯誤的應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/06/2019
uid: blazor/handle-errors
ms.openlocfilehash: 52f55af99881b09c84d9cf88f5845efcb1ea76a1
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948448"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="89004-103">處理 ASP.NET Core Blazor 應用程式中的錯誤</span><span class="sxs-lookup"><span data-stu-id="89004-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="89004-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="89004-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="89004-105">本文說明 Blazor 如何管理未處理的例外狀況, 以及如何開發偵測和處理錯誤的應用程式。</span><span class="sxs-lookup"><span data-stu-id="89004-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="89004-106">Blazor 架構如何回應未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="89004-106">How the Blazor framework reacts to unhandled exceptions</span></span>

<span data-ttu-id="89004-107">Blazor 伺服器端是可設定狀態的架構。</span><span class="sxs-lookup"><span data-stu-id="89004-107">Blazor server-side is a stateful framework.</span></span> <span data-ttu-id="89004-108">當使用者與應用程式互動時, 它們會維持與伺服器的連線, 稱為「*線路*」。</span><span class="sxs-lookup"><span data-stu-id="89004-108">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="89004-109">線路包含作用中的元件實例, 以及其他許多狀態層面, 例如:</span><span class="sxs-lookup"><span data-stu-id="89004-109">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="89004-110">最新呈現的元件輸出。</span><span class="sxs-lookup"><span data-stu-id="89004-110">The most recent rendered output of components.</span></span>
* <span data-ttu-id="89004-111">可由用戶端事件觸發的目前事件處理委派集合。</span><span class="sxs-lookup"><span data-stu-id="89004-111">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="89004-112">如果使用者在多個瀏覽器索引標籤中開啟應用程式, 則會有多個獨立線路。</span><span class="sxs-lookup"><span data-stu-id="89004-112">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="89004-113">Blazor 會將大部分未處理的例外狀況視為其發生所在之線路的嚴重問題。</span><span class="sxs-lookup"><span data-stu-id="89004-113">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="89004-114">如果線路因未處理的例外狀況而終止, 則使用者只需重載頁面來建立新的線路, 即可繼續與應用程式互動。</span><span class="sxs-lookup"><span data-stu-id="89004-114">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="89004-115">在終止的電路以外的線路 (也就是其他使用者或其他瀏覽器索引標籤的線路) 不受影響。</span><span class="sxs-lookup"><span data-stu-id="89004-115">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="89004-116">此案例類似于將損毀&mdash;的應用程式損毀的桌面應用程式, 必須重新開機, 但不會影響其他應用程式。</span><span class="sxs-lookup"><span data-stu-id="89004-116">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="89004-117">當未處理的例外狀況發生時, 會終止電路, 原因如下:</span><span class="sxs-lookup"><span data-stu-id="89004-117">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="89004-118">未處理的例外狀況通常會使線路處於未定義的狀態。</span><span class="sxs-lookup"><span data-stu-id="89004-118">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="89004-119">無法在未處理的例外狀況之後保證應用程式的一般作業。</span><span class="sxs-lookup"><span data-stu-id="89004-119">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="89004-120">如果線路繼續進行, 應用程式中可能會出現安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="89004-120">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="89004-121">在開發人員程式碼中管理未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="89004-121">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="89004-122">若要讓應用程式在發生錯誤後繼續, 應用程式必須有錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="89004-122">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="89004-123">本文稍後的章節會描述未處理之例外狀況的可能來源。</span><span class="sxs-lookup"><span data-stu-id="89004-123">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="89004-124">在生產環境中, 請勿轉譯架構例外狀況訊息, 或在 UI 中呈現堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="89004-124">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="89004-125">呈現例外狀況訊息或堆疊追蹤可能:</span><span class="sxs-lookup"><span data-stu-id="89004-125">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="89004-126">向終端使用者公開機密資訊。</span><span class="sxs-lookup"><span data-stu-id="89004-126">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="89004-127">協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。</span><span class="sxs-lookup"><span data-stu-id="89004-127">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="89004-128">使用持續性提供者記錄錯誤</span><span class="sxs-lookup"><span data-stu-id="89004-128">Log errors with a persistent provider</span></span>

<span data-ttu-id="89004-129">如果發生未處理的例外狀況, 則會將<xref:Microsoft.Extensions.Logging.ILogger>例外狀況記錄到服務容器中設定的實例。</span><span class="sxs-lookup"><span data-stu-id="89004-129">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="89004-130">根據預設, Blazor apps 會使用主控台記錄提供者來記錄至主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="89004-130">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="89004-131">請考慮使用可記錄管理大小和記錄輪替的提供者, 記錄到更永久的位置。</span><span class="sxs-lookup"><span data-stu-id="89004-131">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="89004-132">如需詳細資訊，請參閱 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="89004-132">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="89004-133">在開發期間, Blazor 通常會將例外狀況的完整詳細資料傳送至瀏覽器的主控台, 以協助進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="89004-133">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="89004-134">在生產環境中, 瀏覽器主控台中的詳細錯誤預設為停用, 這表示錯誤不會傳送至用戶端, 但例外狀況的完整詳細資料仍會記錄在伺服器端。</span><span class="sxs-lookup"><span data-stu-id="89004-134">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="89004-135">如需詳細資訊，請參閱 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="89004-135">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="89004-136">您必須決定要記錄哪些事件, 以及記錄事件的嚴重性層級。</span><span class="sxs-lookup"><span data-stu-id="89004-136">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="89004-137">惡意的使用者可能可以故意觸發錯誤。</span><span class="sxs-lookup"><span data-stu-id="89004-137">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="89004-138">例如, 請勿從顯示產品詳細資料之元件的 URL 中`ProductId`提供不明的錯誤中記錄事件。</span><span class="sxs-lookup"><span data-stu-id="89004-138">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="89004-139">並非所有錯誤都應該視為高嚴重性事件以進行記錄。</span><span class="sxs-lookup"><span data-stu-id="89004-139">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="89004-140">可能發生錯誤的位置</span><span class="sxs-lookup"><span data-stu-id="89004-140">Places where errors may occur</span></span>

<span data-ttu-id="89004-141">架構和應用程式程式碼可能會在下列任何位置觸發未處理的例外狀況:</span><span class="sxs-lookup"><span data-stu-id="89004-141">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="89004-142">元件具現化</span><span class="sxs-lookup"><span data-stu-id="89004-142">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="89004-143">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="89004-143">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="89004-144">呈現邏輯</span><span class="sxs-lookup"><span data-stu-id="89004-144">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="89004-145">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="89004-145">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="89004-146">元件處置</span><span class="sxs-lookup"><span data-stu-id="89004-146">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="89004-147">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="89004-147">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="89004-148">線路處理常式</span><span class="sxs-lookup"><span data-stu-id="89004-148">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="89004-149">線路處置</span><span class="sxs-lookup"><span data-stu-id="89004-149">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="89004-150">呈現</span><span class="sxs-lookup"><span data-stu-id="89004-150">Prerendering</span></span>](#prerendering)

<span data-ttu-id="89004-151">前述未處理的例外狀況會在本文的下列各節中說明。</span><span class="sxs-lookup"><span data-stu-id="89004-151">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="89004-152">元件具現化</span><span class="sxs-lookup"><span data-stu-id="89004-152">Component instantiation</span></span>

<span data-ttu-id="89004-153">當 Blazor 建立元件的實例時:</span><span class="sxs-lookup"><span data-stu-id="89004-153">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="89004-154">會叫用元件的函式。</span><span class="sxs-lookup"><span data-stu-id="89004-154">The component's constructor is invoked.</span></span>
* <span data-ttu-id="89004-155">系統會叫用透過指示詞或[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) [[插入]](xref:blazor/dependency-injection#request-a-service-in-a-component)屬性提供給元件之函式的任何非 singleton DI 服務的函式。</span><span class="sxs-lookup"><span data-stu-id="89004-155">The constructors of any non-singleton DI services supplied to the component's constructor via the [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [[Inject]](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="89004-156">任何`[Inject]`屬性的任何執行的函式或 setter 擲回未處理的例外狀況時, 線路都會失敗。</span><span class="sxs-lookup"><span data-stu-id="89004-156">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="89004-157">例外狀況是嚴重的, 因為架構無法具現化元件。</span><span class="sxs-lookup"><span data-stu-id="89004-157">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="89004-158">如果函式邏輯可能會擲回例外狀況, 則應用程式應該使用具有錯誤處理和記錄功能的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)語句來捕捉例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-158">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="89004-159">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="89004-159">Lifecycle methods</span></span>

<span data-ttu-id="89004-160">在元件的存留期間, Blazor 會叫用生命週期方法:</span><span class="sxs-lookup"><span data-stu-id="89004-160">During the lifetime of a component, Blazor invokes lifecycle methods:</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="89004-161">如果任何生命週期方法以同步或非同步方式擲回例外狀況, 則例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-161">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="89004-162">若要讓元件處理生命週期方法中的錯誤, 請新增錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="89004-162">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="89004-163">在下列範例中, `OnParametersSetAsync`會呼叫方法來取得產品:</span><span class="sxs-lookup"><span data-stu-id="89004-163">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="89004-164">`ProductRepository.GetProductByIdAsync`方法中擲回的例外狀況是`try-catch`由語句處理。</span><span class="sxs-lookup"><span data-stu-id="89004-164">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="89004-165">`catch`執行區塊時:</span><span class="sxs-lookup"><span data-stu-id="89004-165">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="89004-166">`loadFailed`設定為`true`, 用來向使用者顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="89004-166">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="89004-167">會記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="89004-167">The error is logged.</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="89004-168">呈現邏輯</span><span class="sxs-lookup"><span data-stu-id="89004-168">Rendering logic</span></span>

<span data-ttu-id="89004-169">`.razor`元件檔案中的宣告式標記會編譯成名C# `BuildRenderTree`為的方法。</span><span class="sxs-lookup"><span data-stu-id="89004-169">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="89004-170">當元件轉譯時, `BuildRenderTree`會執行並建立資料結構, 以描述所轉譯元件的元素、文字和子元件。</span><span class="sxs-lookup"><span data-stu-id="89004-170">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="89004-171">轉譯邏輯可能會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-171">Rendering logic can throw an exception.</span></span> <span data-ttu-id="89004-172">當`@someObject.PropertyName`評估但`@someObject`為`null`時, 就會發生此案例的範例。</span><span class="sxs-lookup"><span data-stu-id="89004-172">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="89004-173">轉譯邏輯所擲回的未處理例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-173">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="89004-174">若要避免轉譯邏輯中出現 null 參考例外狀況, 請`null`先檢查物件, 然後再存取其成員。</span><span class="sxs-lookup"><span data-stu-id="89004-174">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="89004-175">在下列範例中, `person.Address`如果`person.Address`是, 則不`null`會存取屬性:</span><span class="sxs-lookup"><span data-stu-id="89004-175">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="89004-176">上述程式`person`代碼假設不是`null`。</span><span class="sxs-lookup"><span data-stu-id="89004-176">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="89004-177">通常, 程式碼的結構會保證在呈現元件時, 物件存在。</span><span class="sxs-lookup"><span data-stu-id="89004-177">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="89004-178">在這些情況下, 不需要檢查`null`呈現邏輯。</span><span class="sxs-lookup"><span data-stu-id="89004-178">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="89004-179">在先前的範例中`person` , 可能會保證存在, `person`因為會在元件具現化時建立。</span><span class="sxs-lookup"><span data-stu-id="89004-179">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="89004-180">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="89004-180">Event handlers</span></span>

<span data-ttu-id="89004-181">當事件處理常式是使用建立C#時, 用戶端程式代碼會觸發程式碼的調用:</span><span class="sxs-lookup"><span data-stu-id="89004-181">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="89004-182">其他`@on...`屬性</span><span class="sxs-lookup"><span data-stu-id="89004-182">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="89004-183">在這些情況下, 事件處理常式程式碼可能會擲回未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-183">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="89004-184">如果事件處理常式擲回未處理的例外狀況 (例如, 資料庫查詢失敗), 則例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-184">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="89004-185">如果應用程式呼叫可能因外部原因而失敗的程式碼, 請使用具有錯誤處理和記錄的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)語句來設陷例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-185">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="89004-186">如果使用者程式碼不會陷印並處理例外狀況, 則架構會記錄例外狀況並終止線路。</span><span class="sxs-lookup"><span data-stu-id="89004-186">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="89004-187">元件處置</span><span class="sxs-lookup"><span data-stu-id="89004-187">Component disposal</span></span>

<span data-ttu-id="89004-188">例如, 元件可能會從 UI 移除, 因為使用者已流覽至另一個頁面。</span><span class="sxs-lookup"><span data-stu-id="89004-188">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="89004-189">當執行的元件<xref:System.IDisposable?displayProperty=fullName>從 UI 中移除時, 架構會呼叫元件的<xref:System.IDisposable.Dispose*>方法。</span><span class="sxs-lookup"><span data-stu-id="89004-189">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="89004-190">如果元件的`Dispose`方法擲回未處理的例外狀況, 則例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-190">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="89004-191">如果處置邏輯可能會擲回例外狀況, 則應用程式應該使用具有錯誤處理和記錄的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)語句來捕捉例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-191">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="89004-192">如需有關元件處置的詳細資訊<xref:blazor/components#component-disposal-with-idisposable>, 請參閱。</span><span class="sxs-lookup"><span data-stu-id="89004-192">For more information on component disposal, see <xref:blazor/components#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="89004-193">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="89004-193">JavaScript interop</span></span>

<span data-ttu-id="89004-194">`IJSRuntime.InvokeAsync<T>`允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="89004-194">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="89004-195">下列條件適用于使用`InvokeAsync<T>`的錯誤處理:</span><span class="sxs-lookup"><span data-stu-id="89004-195">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="89004-196">如果對的呼叫`InvokeAsync<T>`同步失敗, 就會發生 .net 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-196">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="89004-197">例如, 對`InvokeAsync<T>`的呼叫失敗, 因為無法序列化提供的引數。</span><span class="sxs-lookup"><span data-stu-id="89004-197">A call to `InvokeAsync<T>` my fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="89004-198">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-198">Developer code must catch the exception.</span></span> <span data-ttu-id="89004-199">如果事件處理常式或元件生命週期方法中的應用程式程式碼不會處理例外狀況, 則產生的例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-199">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="89004-200">如果的呼叫非同步`InvokeAsync<T>`失敗, .net <xref:System.Threading.Tasks.Task>就會失敗。</span><span class="sxs-lookup"><span data-stu-id="89004-200">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="89004-201">例如, 對`InvokeAsync<T>`的呼叫可能會失敗, 因為 JavaScript 端程式碼會擲回例外狀況, 或`Promise`傳回以形式`rejected`完成的。</span><span class="sxs-lookup"><span data-stu-id="89004-201">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="89004-202">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-202">Developer code must catch the exception.</span></span> <span data-ttu-id="89004-203">如果使用[await](/dotnet/csharp/language-reference/keywords/await)運算子, 請考慮將方法呼叫包裝在含有錯誤處理和記錄的[try catch](/dotnet/csharp/language-reference/keywords/try-catch)語句中。</span><span class="sxs-lookup"><span data-stu-id="89004-203">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="89004-204">否則, 失敗的程式碼會產生對線路而言是嚴重的未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-204">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="89004-205">根據預設, 的呼叫`InvokeAsync<T>`必須在特定期間內完成, 否則呼叫會超時。預設的超時時間為一分鐘。</span><span class="sxs-lookup"><span data-stu-id="89004-205">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="89004-206">Timeout 會保護程式碼不會遺失網路連線, 或永遠不會傳回完成訊息的 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="89004-206">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="89004-207">如果呼叫超時, 則產生`Task`的會失敗<xref:System.OperationCanceledException>並出現。</span><span class="sxs-lookup"><span data-stu-id="89004-207">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="89004-208">使用記錄來設陷並處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-208">Trap and process the exception with logging.</span></span>

<span data-ttu-id="89004-209">同樣地, JavaScript 程式碼可能會起始對[[JSInvokable] 屬性](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)所指示之 .net 方法的呼叫。</span><span class="sxs-lookup"><span data-stu-id="89004-209">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [[JSInvokable] attribute](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions).</span></span> <span data-ttu-id="89004-210">如果這些 .NET 方法擲回未處理的例外狀況:</span><span class="sxs-lookup"><span data-stu-id="89004-210">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="89004-211">此例外狀況不會被視為對線路的嚴重錯誤。</span><span class="sxs-lookup"><span data-stu-id="89004-211">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="89004-212">JavaScript 端`Promise`遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="89004-212">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="89004-213">您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。</span><span class="sxs-lookup"><span data-stu-id="89004-213">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="89004-214">如需詳細資訊，請參閱 <xref:blazor/javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="89004-214">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="89004-215">線路處理常式</span><span class="sxs-lookup"><span data-stu-id="89004-215">Circuit handlers</span></span>

<span data-ttu-id="89004-216">Blazor 可讓程式碼定義*電路處理常式*, 以在使用者的線路狀態變更時接收通知。</span><span class="sxs-lookup"><span data-stu-id="89004-216">Blazor allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="89004-217">使用下列狀態:</span><span class="sxs-lookup"><span data-stu-id="89004-217">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="89004-218">通知的管理方式是註冊繼承自`CircuitHandler`抽象基類的 DI 服務。</span><span class="sxs-lookup"><span data-stu-id="89004-218">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="89004-219">如果自訂電路處理常式的方法擲回未處理的例外狀況, 則例外狀況對線路而言是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="89004-219">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="89004-220">若要容忍處理常式程式碼或呼叫方法中的例外狀況, 請使用錯誤處理和記錄, 將程式碼包裝在一個或多個[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)語句中。</span><span class="sxs-lookup"><span data-stu-id="89004-220">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="89004-221">線路處置</span><span class="sxs-lookup"><span data-stu-id="89004-221">Circuit disposal</span></span>

<span data-ttu-id="89004-222">當線路因使用者已中斷連線而結束, 而架構正在清除線路狀態時, 架構會處置線路的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="89004-222">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="89004-223">處置範圍會處置任何執行<xref:System.IDisposable?displayProperty=fullName>的線路範圍 DI 服務。</span><span class="sxs-lookup"><span data-stu-id="89004-223">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="89004-224">如果任何 DI 服務在處置期間擲回未處理的例外狀況, 則架構會記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-224">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="89004-225">呈現</span><span class="sxs-lookup"><span data-stu-id="89004-225">Prerendering</span></span>

<span data-ttu-id="89004-226">您可以使用`Html.RenderComponentAsync`來資源清單 Blazor 元件, 使其呈現的 HTML 標籤會當做使用者初始 HTTP 要求的一部分傳回。</span><span class="sxs-lookup"><span data-stu-id="89004-226">Blazor components can be prerendered using `Html.RenderComponentAsync` so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="89004-227">其運作方式如下:</span><span class="sxs-lookup"><span data-stu-id="89004-227">This works by:</span></span>

* <span data-ttu-id="89004-228">建立新的電路, 其中包含屬於相同頁面的所有資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="89004-228">Creating a new circuit containing all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="89004-229">產生初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="89004-229">Generating the initial HTML.</span></span>
* <span data-ttu-id="89004-230">將線路`disconnected`視為, 直到使用者的瀏覽器建立 SignalR 連線回到同一部伺服器, 以恢復線路上的互動性。</span><span class="sxs-lookup"><span data-stu-id="89004-230">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server to resume interactivity on the circuit.</span></span>

<span data-ttu-id="89004-231">如果任何元件在進行預入期間擲回未處理的例外狀況 (例如, 在生命週期方法或轉譯邏輯期間):</span><span class="sxs-lookup"><span data-stu-id="89004-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="89004-232">這是電路的嚴重例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="89004-233">從`Html.RenderComponentAsync`呼叫中擲回呼叫堆疊的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="89004-233">The exception is thrown up the call stack from the `Html.RenderComponentAsync` call.</span></span> <span data-ttu-id="89004-234">因此, 除非開發人員程式碼明確攔截到例外狀況, 否則整個 HTTP 要求都會失敗。</span><span class="sxs-lookup"><span data-stu-id="89004-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="89004-235">在一般情況下, 如果無法轉譯, 則繼續建立並轉譯元件並沒有意義, 因為無法轉譯運作中的元件。</span><span class="sxs-lookup"><span data-stu-id="89004-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="89004-236">若要容忍在自動處理期間可能發生的錯誤, 錯誤處理邏輯必須放在可能會擲回例外狀況的元件內部。</span><span class="sxs-lookup"><span data-stu-id="89004-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="89004-237">使用[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)語句搭配錯誤處理和記錄。</span><span class="sxs-lookup"><span data-stu-id="89004-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="89004-238">不要將呼叫`RenderComponentAsync`包裝`try-catch`在語句中, 而是將錯誤處理邏輯放`RenderComponentAsync`在所呈現的元件中。</span><span class="sxs-lookup"><span data-stu-id="89004-238">Instead of wrapping the call to `RenderComponentAsync` in a `try-catch` statement, place error handling logic in the component rendered by `RenderComponentAsync`.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="89004-239">Advanced 案例</span><span class="sxs-lookup"><span data-stu-id="89004-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="89004-240">遞迴轉譯</span><span class="sxs-lookup"><span data-stu-id="89004-240">Recursive rendering</span></span>

<span data-ttu-id="89004-241">元件可以遞迴方式進行嵌套。</span><span class="sxs-lookup"><span data-stu-id="89004-241">Components can be nested recursively.</span></span> <span data-ttu-id="89004-242">這適用于表示遞迴資料結構。</span><span class="sxs-lookup"><span data-stu-id="89004-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="89004-243">例如, `TreeNode`元件可以呈現每個節點`TreeNode`子系的更多元件。</span><span class="sxs-lookup"><span data-stu-id="89004-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="89004-244">以遞迴方式轉譯時, 避免產生無限遞迴的編碼模式:</span><span class="sxs-lookup"><span data-stu-id="89004-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="89004-245">不要以遞迴方式呈現包含迴圈的資料結構。</span><span class="sxs-lookup"><span data-stu-id="89004-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="89004-246">例如, 不要轉譯其子系包含其本身的樹狀節點。</span><span class="sxs-lookup"><span data-stu-id="89004-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="89004-247">請勿建立包含迴圈的版面配置鏈。</span><span class="sxs-lookup"><span data-stu-id="89004-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="89004-248">例如, 請勿建立版面配置本身的版面配置。</span><span class="sxs-lookup"><span data-stu-id="89004-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="89004-249">不允許終端使用者透過惡意資料輸入或 JavaScript interop 呼叫來違反遞迴不變數 (規則)。</span><span class="sxs-lookup"><span data-stu-id="89004-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="89004-250">呈現期間的無限迴圈:</span><span class="sxs-lookup"><span data-stu-id="89004-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="89004-251">導致轉譯進程永遠繼續。</span><span class="sxs-lookup"><span data-stu-id="89004-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="89004-252">相當於建立未結束的迴圈。</span><span class="sxs-lookup"><span data-stu-id="89004-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="89004-253">在這些情況下, 受影響的線路會停止回應, 而執行緒通常會嘗試:</span><span class="sxs-lookup"><span data-stu-id="89004-253">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="89004-254">會無限期地耗用作業系統所允許的 CPU 時間量。</span><span class="sxs-lookup"><span data-stu-id="89004-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="89004-255">耗用不限數量的伺服器記憶體。</span><span class="sxs-lookup"><span data-stu-id="89004-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="89004-256">使用無限制的記憶體相當於未結束的迴圈在每次反覆運算時將專案新增至集合的案例。</span><span class="sxs-lookup"><span data-stu-id="89004-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="89004-257">若要避免無限遞迴模式, 請確定遞迴呈現程式碼包含適當的停止條件。</span><span class="sxs-lookup"><span data-stu-id="89004-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="89004-258">自訂呈現樹狀結構邏輯</span><span class="sxs-lookup"><span data-stu-id="89004-258">Custom render tree logic</span></span>

<span data-ttu-id="89004-259">大部分的 Blazor 元件都會實作為*razor*檔案, 並且會進行編譯, 以產生邏輯來`RenderTreeBuilder`操作以轉譯其輸出。</span><span class="sxs-lookup"><span data-stu-id="89004-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="89004-260">開發人員可以使用程式`RenderTreeBuilder`性程式C#代碼手動執行邏輯。</span><span class="sxs-lookup"><span data-stu-id="89004-260">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="89004-261">如需詳細資訊，請參閱 <xref:blazor/components#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="89004-261">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="89004-262">手動轉譯樹狀結構產生器邏輯的使用會被視為先進且不安全的案例, 不建議用於一般元件開發。</span><span class="sxs-lookup"><span data-stu-id="89004-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="89004-263">撰寫`RenderTreeBuilder`程式碼時, 開發人員必須保證程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="89004-263">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="89004-264">例如, 開發人員必須確定:</span><span class="sxs-lookup"><span data-stu-id="89004-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="89004-265">對`OpenElement` 和`CloseElement`的呼叫已正確平衡。</span><span class="sxs-lookup"><span data-stu-id="89004-265">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="89004-266">屬性只會加入正確的位置。</span><span class="sxs-lookup"><span data-stu-id="89004-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="89004-267">不正確的手動轉譯樹狀結構產生器邏輯可能會造成任意未定義的行為, 包括損毀、伺服器停止回應和安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="89004-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="89004-268">請考慮以手動方式呈現相同的複雜性層級的轉譯樹產生器邏輯, 以及使用與手動撰寫元件程式碼或 MSIL 指令相同層級的*危險*。</span><span class="sxs-lookup"><span data-stu-id="89004-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>