---
title: 裝載和部署 Blazor 用戶端
author: guardrex
description: 了解如何使用 ASP.NET Core、內容傳遞網路 (CDN)、檔案伺服器和 GitHub Pages 來裝載和部署 Blazor 應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 01a612029f415f583908c3bf2adc2e6d35167acb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614661"
---
# <a name="host-and-deploy-blazor-client-side"></a><span data-ttu-id="64997-103">裝載和部署 Blazor 用戶端</span><span class="sxs-lookup"><span data-stu-id="64997-103">Host and deploy Blazor client-side</span></span>

<span data-ttu-id="64997-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="64997-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="host-configuration-values"></a><span data-ttu-id="64997-105">主機組態值</span><span class="sxs-lookup"><span data-stu-id="64997-105">Host configuration values</span></span>

<span data-ttu-id="64997-106">使用[用戶端裝載模型](xref:blazor/hosting-models#client-side-hosting-model)的 Blazor 應用程式，可以在開發環境中，在執行時接受下列主機組態值作為命令列引數。</span><span class="sxs-lookup"><span data-stu-id="64997-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="64997-107">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="64997-107">Content Root</span></span>

<span data-ttu-id="64997-108">`--contentroot` 引數設定包含應用程式內容檔案的目錄絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="64997-109">在下列範例中，`/content-root-path` 是應用程式的內容根路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="64997-110">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="64997-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="64997-111">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="64997-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="64997-112">新增項目至應用程式 **IIS Express** 設定檔中的 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="64997-113">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="64997-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="64997-114">在 Visual Studio 中，在 [屬性] > [偵錯] > [應用程式引數] 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="64997-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="64997-115">在 Visual Studio 屬性頁中設定引數，會將引數新增至 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="64997-116">路徑基底</span><span class="sxs-lookup"><span data-stu-id="64997-116">Path base</span></span>

<span data-ttu-id="64997-117">`--pathbase` 引數會以非根虛擬路徑，為本機執行的應用程式設定應用程式基底路徑 (`<base>` 標籤 `href` 會設為非 `/` 的路徑以供預備和生產之用)。</span><span class="sxs-lookup"><span data-stu-id="64997-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="64997-118">在下列範例中，`/virtual-path` 是應用程式的路徑基底。</span><span class="sxs-lookup"><span data-stu-id="64997-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="64997-119">如需詳細資訊，請參閱[應用程式基底路徑](#app-base-path)一節。</span><span class="sxs-lookup"><span data-stu-id="64997-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="64997-120">不同於提供給 `<base>` 標籤 `href` 的路徑，在傳遞 `--pathbase` 引數值時請勿包含尾端的斜線 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="64997-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="64997-121">如果應用程式基底路徑提供於 `<base>` 標籤作為 `<base href="/CoolApp/">` (包括尾端的斜線)，請將命令列引數值傳遞為 `--pathbase=/CoolApp` (不含尾端的斜線)。</span><span class="sxs-lookup"><span data-stu-id="64997-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="64997-122">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="64997-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="64997-123">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="64997-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="64997-124">新增項目至應用程式 **IIS Express** 設定檔中的 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="64997-125">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="64997-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="64997-126">在 Visual Studio 中，在 [屬性] > [偵錯] > [應用程式引數] 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="64997-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="64997-127">在 Visual Studio 屬性頁中設定引數，會將引數新增至 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="64997-128">URL</span><span class="sxs-lookup"><span data-stu-id="64997-128">URLs</span></span>

<span data-ttu-id="64997-129">`--urls` 引數會搭配要用來接聽要求的連接埠和通訊協定，設定 IP 位址或主機位址。</span><span class="sxs-lookup"><span data-stu-id="64997-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="64997-130">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="64997-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="64997-131">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="64997-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="64997-132">新增項目至應用程式 **IIS Express** 設定檔中的 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="64997-133">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="64997-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="64997-134">在 Visual Studio 中，在 [屬性] > [偵錯] > [應用程式引數] 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="64997-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="64997-135">在 Visual Studio 屬性頁中設定引數，會將引數新增至 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="64997-136">部署</span><span class="sxs-lookup"><span data-stu-id="64997-136">Deployment</span></span>

<span data-ttu-id="64997-137">使用[用戶端裝載模型](xref:blazor/hosting-models#client-side-hosting-model)：</span><span class="sxs-lookup"><span data-stu-id="64997-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="64997-138">Blazor 應用程式、其相依性，和 .NET 執行階段會下載至瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="64997-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="64997-139">應用程式會直接在瀏覽器 UI 執行緒上執行。</span><span class="sxs-lookup"><span data-stu-id="64997-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="64997-140">支援下列策略之一：</span><span class="sxs-lookup"><span data-stu-id="64997-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="64997-141">Blazor 應用程式由 ASP.NET Core 應用程式提供服務。</span><span class="sxs-lookup"><span data-stu-id="64997-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="64997-142">此策略已於[搭配 ASP.NET Core 的已裝載部署](#hosted-deployment-with-aspnet-core)一節中涵蓋。</span><span class="sxs-lookup"><span data-stu-id="64997-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="64997-143">Blazor 應用程式放在靜態裝載的網頁伺服器或服務上，其中 .NET 不會用來提供服務給 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="64997-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="64997-144">此策略已於[獨立部署](#standalone-deployment)一節中涵蓋。</span><span class="sxs-lookup"><span data-stu-id="64997-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="64997-145">設定連結器</span><span class="sxs-lookup"><span data-stu-id="64997-145">Configure the Linker</span></span>

<span data-ttu-id="64997-146">Blazor 在每個組建上執行中繼語言 (IL) 連結，以從輸出組件移除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="64997-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="64997-147">組件連結可在組建上控制。</span><span class="sxs-lookup"><span data-stu-id="64997-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="64997-148">如需詳細資訊，請參閱<xref:host-and-deploy/blazor/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="64997-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="64997-149">重寫 URL 以便正確地路由</span><span class="sxs-lookup"><span data-stu-id="64997-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="64997-150">用戶端應用程式中的頁面元件傳送要求，並不只是將要求傳送到伺服器端的裝載應用程式那麼簡單。</span><span class="sxs-lookup"><span data-stu-id="64997-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="64997-151">請考慮具有兩個頁面的用戶端應用程式：</span><span class="sxs-lookup"><span data-stu-id="64997-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="64997-152">**_Main.cshtml_** &ndash; 在應用程式根目錄載入，並包含 [關於] 頁面的連結 (`href="About"`)。</span><span class="sxs-lookup"><span data-stu-id="64997-152">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="64997-153">**_About.cshtml_** &ndash; [關於] 頁面。</span><span class="sxs-lookup"><span data-stu-id="64997-153">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="64997-154">使用瀏覽器的網址列要求應用程式預設文件時 (例如 `https://www.contoso.com/`)：</span><span class="sxs-lookup"><span data-stu-id="64997-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="64997-155">瀏覽器提出要求。</span><span class="sxs-lookup"><span data-stu-id="64997-155">The browser makes a request.</span></span>
1. <span data-ttu-id="64997-156">傳回預設頁面，這通常是 *index.html*。</span><span class="sxs-lookup"><span data-stu-id="64997-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="64997-157">*index.html* 啟動載入應用程式。</span><span class="sxs-lookup"><span data-stu-id="64997-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="64997-158">這會載入 Blazor 的路由器，且 Razor 主頁面 (*Main.cshtml*) 隨即出現。</span><span class="sxs-lookup"><span data-stu-id="64997-158">Blazor's router loads, and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="64997-159">在主頁面上，選取 [關於] 頁面的連結會載入 [關於] 頁面。</span><span class="sxs-lookup"><span data-stu-id="64997-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="64997-160">選取您用戶端上適用的 [關於] 頁面連結，因為 Blazor 路由器會停止瀏覽器，使其不從網際網路對 `www.contoso.com` 提出針對 `About` 的要求，並自行提供 [關於] 頁面。</span><span class="sxs-lookup"><span data-stu-id="64997-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="64997-161">對於「用戶端應用程式內」內部頁面的所有要求運作方式相同：要求不會對於網際網路上伺服器裝載的資源，觸發以瀏覽器為基礎的要求。</span><span class="sxs-lookup"><span data-stu-id="64997-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="64997-162">路由器會在內部處理要求。</span><span class="sxs-lookup"><span data-stu-id="64997-162">The router handles the requests internally.</span></span>

<span data-ttu-id="64997-163">如果使用瀏覽器之網址列提出對 `www.contoso.com/About` 的要求，則要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="64997-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="64997-164">在應用程式的網際網路主機上沒有這類資源存在，因此會傳回「404 - 找不到」的回應。</span><span class="sxs-lookup"><span data-stu-id="64997-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="64997-165">因為瀏覽器會提出要求給以網際網路為基礎的主機以取得用戶端頁面，所以網頁伺服器和裝載服務必須針對實際上不在伺服器上資源，將所有要求重新撰寫至 *index.html* 頁面。</span><span class="sxs-lookup"><span data-stu-id="64997-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="64997-166">傳回 *index.html* 時，應用程式的用戶端路由器會接管，並以正確的資源回應。</span><span class="sxs-lookup"><span data-stu-id="64997-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="64997-167">應用程式基底路徑</span><span class="sxs-lookup"><span data-stu-id="64997-167">App base path</span></span>

<span data-ttu-id="64997-168">「應用程式基底路徑」是伺服器上的虛擬應用程式根路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="64997-169">例如，對於位於 Contoso 伺服器虛擬資料夾 `/CoolApp/` 的應用程式，能使用 `https://www.contoso.com/CoolApp` 到達它，且具有 `/CoolApp/` 虛擬基底路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="64997-170">藉由將應用程式基底路徑設為 `CoolApp/`，應用程式會察覺它虛擬地位於伺服器上何處。</span><span class="sxs-lookup"><span data-stu-id="64997-170">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="64997-171">應用程式可以使用應用程式基底路徑，從不在根目錄中之元件建構相對於應用程式根目錄的 URL。</span><span class="sxs-lookup"><span data-stu-id="64997-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="64997-172">這可讓存在於不同目錄結構層級的元件，建置連結以連至應用程式內所有位置的其他資源。</span><span class="sxs-lookup"><span data-stu-id="64997-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="64997-173">應用程式基底路徑也會用來攔截超連結點擊，其中連結的 `href` 目標是在應用程式基底路徑 URI 空間內&mdash;Blazor 路由器會處理內部瀏覽。</span><span class="sxs-lookup"><span data-stu-id="64997-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="64997-174">在許多裝載案例中，應用程式之伺服器虛擬路徑是應用程式的根目錄。</span><span class="sxs-lookup"><span data-stu-id="64997-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="64997-175">在這些情況中，應用程式基底路徑是正斜線 (`<base href="/" />`)，這是應用程式的預設組態。</span><span class="sxs-lookup"><span data-stu-id="64997-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="64997-176">在其他裝載案例中，例如 GitHub Pages 和 IIS 虛擬目錄或子應用程式，應用程式基底路徑必須設定為應用程式的伺服器虛擬路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="64997-177">若要設定應用程式的基底路徑，請在 *index.html* 中新增或更新 `<base>` 標籤，這可在 `<head>` 標籤項目內找到。</span><span class="sxs-lookup"><span data-stu-id="64997-177">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="64997-178">將 `href` 屬性值設為 `virtual-path/` (尾端的斜線為必要)，其中 `virtual-path/` 是應用程式伺服器上的完整虛擬應用程式根路徑。</span><span class="sxs-lookup"><span data-stu-id="64997-178">Set the `href` attribute value to `virtual-path/` (the trailing slash is required), where `virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="64997-179">在上述範例中，虛擬路徑設為 `CoolApp/`: `<base href="CoolApp/">`。</span><span class="sxs-lookup"><span data-stu-id="64997-179">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/">`.</span></span>

<span data-ttu-id="64997-180">對於已設定非根目錄虛擬路徑的應用程式 (例如 `<base href="CoolApp/">`)，應用程式「在本機執行時」無法找到它的資源。</span><span class="sxs-lookup"><span data-stu-id="64997-180">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="64997-181">若要在本機開發和測試期間解決這個問題，您可以提供「基底路徑」引數，讓它在執行時符合 `<base>` 標籤的 `href` 值。</span><span class="sxs-lookup"><span data-stu-id="64997-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="64997-182">若要在本機執行應用程式時傳遞路徑基底引數與根路徑 (`/`)，請從應用程式的目錄執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="64997-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="64997-183">應用程式在 `http://localhost:port/CoolApp` 本機回應。</span><span class="sxs-lookup"><span data-stu-id="64997-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="64997-184">如需詳細資訊，請參閱[路徑基底主機設定值](#path-base)一節。</span><span class="sxs-lookup"><span data-stu-id="64997-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="64997-185">如果應用程式使用[用戶端裝載模型](xref:blazor/hosting-models#client-side-hosting-model) (根據 **Blazor** 專案範本)，且裝載為 ASP.NET Core 應用程式中的 IIS 子應用程式，請務必停用繼承的 ASP.NET Core 模組處理常式，或是確定 *web.config* 檔案中，根 (父系) 應用程式的 `<handlers>` 區段不由子應用程式繼承。</span><span class="sxs-lookup"><span data-stu-id="64997-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="64997-186">移除應用程式已發佈 *web.config* 檔案中的處理常式，方法是新增 `<handlers>` 區段到檔案：</span><span class="sxs-lookup"><span data-stu-id="64997-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="64997-187">或者，使用 `<location>` 項目，並將 `inheritInChildApplications` 設為 `false`，以停用根 (父系) 應用程式的 `<system.webServer>` 區段繼承：</span><span class="sxs-lookup"><span data-stu-id="64997-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="64997-188">除了如本節中所述設定應用程式的基底路徑，也會執行移除處理常式或停用繼承。</span><span class="sxs-lookup"><span data-stu-id="64997-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="64997-189">在應用程式的 *index.html* 檔案，將應用程式基底路徑設為在 IIS 中設定子應用程式時使用的 IIS 別名。</span><span class="sxs-lookup"><span data-stu-id="64997-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="64997-190">搭配 ASP.NET Core 的已裝載部署</span><span class="sxs-lookup"><span data-stu-id="64997-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="64997-191">「裝載部署」會將用戶端 Blazor 應用程式，從伺服器上執行的 [ASP.NET Core 應用程式](xref:index)，提供給瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="64997-191">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="64997-192">Blazor 應用程式隨附於發佈輸出中的 ASP.NET Core 應用程式，以便兩個應用程式會一起部署。</span><span class="sxs-lookup"><span data-stu-id="64997-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="64997-193">需要有能夠裝載 ASP.NET Core 應用程式的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="64997-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="64997-194">對於裝載部署，Visual Studio 包含 **Blazor (已裝載 ASP.NET Core)** 專案範本 (使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令時的 `blazorhosted` 範本)。</span><span class="sxs-lookup"><span data-stu-id="64997-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="64997-195">如需 ASP.NET Core 應用程式裝載和部署的詳細資訊，請參閱 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="64997-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="64997-196">如需部署至 Azure App Service 的相關資訊，請參閱 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="64997-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="64997-197">獨立部署</span><span class="sxs-lookup"><span data-stu-id="64997-197">Standalone deployment</span></span>

<span data-ttu-id="64997-198">「獨立部署」會以一組直接由用戶端要求的靜態檔案來提供服務給用戶端 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="64997-198">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="64997-199">網頁伺服器不用來提供服務給 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="64997-199">A web server isn't used to serve the Blazor app.</span></span>

### <a name="iis"></a><span data-ttu-id="64997-200">IIS</span><span class="sxs-lookup"><span data-stu-id="64997-200">IIS</span></span>

<span data-ttu-id="64997-201">IIS 是足以支援 Blazor 應用程式的靜態檔案伺服器。</span><span class="sxs-lookup"><span data-stu-id="64997-201">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="64997-202">若要設定 IIS 來裝載 Blazor，請參閱[在 IIS 上建置靜態網站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="64997-202">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="64997-203">已發行的資產會建立在 */bin/Release/{TARGET FRAMEWORK}/publish* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="64997-203">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="64997-204">在網頁伺服器或裝載服務上，裝載 *publish* 資料夾的內容。</span><span class="sxs-lookup"><span data-stu-id="64997-204">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="64997-205">web.config</span><span class="sxs-lookup"><span data-stu-id="64997-205">web.config</span></span>

<span data-ttu-id="64997-206">發佈 Blazor 專案時，會建立 *web.config* 檔案，並使用下列 IIS 組態：</span><span class="sxs-lookup"><span data-stu-id="64997-206">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="64997-207">針對下列副檔名設定 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="64997-207">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="64997-208">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="64997-208">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="64997-209">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="64997-209">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="64997-210">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="64997-210">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="64997-211">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="64997-211">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="64997-212">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="64997-212">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="64997-213">針對下列 MIME 類型會啟用 HTTP 壓縮：</span><span class="sxs-lookup"><span data-stu-id="64997-213">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="64997-214">建立 URL Rewrite Module 規則：</span><span class="sxs-lookup"><span data-stu-id="64997-214">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="64997-215">提供應用程式靜態資產所在的子目錄 (*{組件名稱}/dist/{要求的路徑}*)。</span><span class="sxs-lookup"><span data-stu-id="64997-215">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="64997-216">建立 SPA 後援路由，讓非檔案資產要求重新導向至其靜態資產資料夾中的應用程式預設文件 (*{組件名稱}/dist/index.html*)。</span><span class="sxs-lookup"><span data-stu-id="64997-216">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="64997-217">安裝 URL Rewrite 模組</span><span class="sxs-lookup"><span data-stu-id="64997-217">Install the URL Rewrite Module</span></span>

<span data-ttu-id="64997-218">需要 [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite)，才可重寫 URL。</span><span class="sxs-lookup"><span data-stu-id="64997-218">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="64997-219">預設不會安裝此模組，且其無法用來安裝為網頁伺服器 (IIS) 角色服務功能。</span><span class="sxs-lookup"><span data-stu-id="64997-219">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="64997-220">必須從 IIS 網站下載模組。</span><span class="sxs-lookup"><span data-stu-id="64997-220">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="64997-221">請使用 Web Platform Installer 安裝模組：</span><span class="sxs-lookup"><span data-stu-id="64997-221">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="64997-222">在本機上，巡覽至 [URL Rewrite Module 下載頁面](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="64997-222">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="64997-223">如需英文版，請選取 [WebPI] 下載 WebPI 安裝程式。</span><span class="sxs-lookup"><span data-stu-id="64997-223">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="64997-224">如需其他語言，請選取適當的伺服器架構 (x86 x64) 來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="64997-224">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="64997-225">將安裝程式複製到伺服器。</span><span class="sxs-lookup"><span data-stu-id="64997-225">Copy the installer to the server.</span></span> <span data-ttu-id="64997-226">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="64997-226">Run the installer.</span></span> <span data-ttu-id="64997-227">選取 [安裝] 按鈕，並接受授權條款。</span><span class="sxs-lookup"><span data-stu-id="64997-227">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="64997-228">安裝完成之後，不需要重新啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="64997-228">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="64997-229">設定網站</span><span class="sxs-lookup"><span data-stu-id="64997-229">Configure the website</span></span>

<span data-ttu-id="64997-230">將網站的 [實體路徑] 設為應用程式的資料夾。</span><span class="sxs-lookup"><span data-stu-id="64997-230">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="64997-231">此資料夾包含：</span><span class="sxs-lookup"><span data-stu-id="64997-231">The folder contains:</span></span>

* <span data-ttu-id="64997-232">IIS 用來設定網站的 *web.config* 檔，包括必要的重新導向規則和檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="64997-232">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="64997-233">應用程式的靜態資產資料夾。</span><span class="sxs-lookup"><span data-stu-id="64997-233">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="64997-234">疑難排解</span><span class="sxs-lookup"><span data-stu-id="64997-234">Troubleshooting</span></span>

<span data-ttu-id="64997-235">如果收到「500 - 內部伺服器錯誤」，且 IIS 管理員在嘗試存取網站設定時擲回錯誤，請確認是否已安裝 URL Rewrite 模組。</span><span class="sxs-lookup"><span data-stu-id="64997-235">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="64997-236">未安裝此模組時，IIS 無法剖析 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-236">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="64997-237">這導致 IIS 管理員無法載入網站的組態，且網站無法提供 Blazor 的靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-237">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="64997-238">如需針對部署至 IIS 進行疑難排解的詳細資訊，請參閱 <xref:host-and-deploy/iis/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="64997-238">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="nginx"></a><span data-ttu-id="64997-239">Nginx</span><span class="sxs-lookup"><span data-stu-id="64997-239">Nginx</span></span>

<span data-ttu-id="64997-240">下列 *nginx.conf* 檔案已簡化，示範如何將 Nginx 設定為每當找不到磁碟上的對應檔案時，便傳送 *Index.html* 檔案。</span><span class="sxs-lookup"><span data-stu-id="64997-240">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="64997-241">如需生產環境 Nginx 網頁伺服器組態的詳細資訊，請參閱[建立 NGINX Plus 和 NGINX 組態檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)。</span><span class="sxs-lookup"><span data-stu-id="64997-241">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="64997-242">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="64997-242">Nginx in Docker</span></span>

<span data-ttu-id="64997-243">若要在 Docker 中使用 Nginx 裝載 Blazor，請設定 Dockerfile 以使用以 Alpine 為基礎的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="64997-243">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="64997-244">更新 Dockerfile，將 *nginx.config* 檔案複製到容器內。</span><span class="sxs-lookup"><span data-stu-id="64997-244">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="64997-245">如下列範例所示，新增一行至 Dockerfile：</span><span class="sxs-lookup"><span data-stu-id="64997-245">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="64997-246">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="64997-246">GitHub Pages</span></span>

<span data-ttu-id="64997-247">若要處理 URL 重寫，請新增 *404.html* 檔，以及處理將要求重新導向至 *index.html* 頁面的指令碼。</span><span class="sxs-lookup"><span data-stu-id="64997-247">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="64997-248">如需社群所提供的範例實作，請參閱 [GitHub Pages 的單一頁面應用程式](http://spa-github-pages.rafrex.com/) (GitHub 上的 [rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme))。</span><span class="sxs-lookup"><span data-stu-id="64997-248">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="64997-249">使用社群方法的範例可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io) ([即時網站](https://blazor-demo.github.io/)) 看到。</span><span class="sxs-lookup"><span data-stu-id="64997-249">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="64997-250">使用專案網站，而非組織網站時，請在 *index.html* 中新增或更新 `<base>` 標籤。</span><span class="sxs-lookup"><span data-stu-id="64997-250">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="64997-251">將 `href` 屬性值設定為 GitHub 存放庫名稱，並在尾端加上斜線 (例如 `my-repository/`)。</span><span class="sxs-lookup"><span data-stu-id="64997-251">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>