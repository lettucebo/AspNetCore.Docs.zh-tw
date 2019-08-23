---
title: ASP.NET Core Blazor 表單和驗證
author: guardrex
description: 瞭解如何在 Blazor 中使用表單和欄位驗證案例。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030333"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="7ab29-103">ASP.NET Core Blazor 表單和驗證</span><span class="sxs-lookup"><span data-stu-id="7ab29-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="7ab29-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ab29-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ab29-105">使用[資料批註](xref:mvc/models/validation)的 Blazor 支援表單和驗證。</span><span class="sxs-lookup"><span data-stu-id="7ab29-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="7ab29-106">每個預覽版本可能會變更表單和驗證案例。</span><span class="sxs-lookup"><span data-stu-id="7ab29-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="7ab29-107">下列`ExampleModel`型別會使用資料批註來定義驗證邏輯:</span><span class="sxs-lookup"><span data-stu-id="7ab29-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="7ab29-108">表單是使用`EditForm`元件定義的。</span><span class="sxs-lookup"><span data-stu-id="7ab29-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="7ab29-109">下列表單會示範典型的元素、元件和 Razor 程式碼:</span><span class="sxs-lookup"><span data-stu-id="7ab29-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="7ab29-110">表單會使用在`name` `ExampleModel`型別中定義的驗證來驗證欄位中的使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="7ab29-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="7ab29-111">模型會建立在元件的`@code`區塊中, 並保留在私用欄位 (`exampleModel`) 中。</span><span class="sxs-lookup"><span data-stu-id="7ab29-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="7ab29-112">欄位會指派`Model`給`<EditForm>`元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="7ab29-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="7ab29-113">`DataAnnotationsValidator`元件會使用資料批註附加驗證支援。</span><span class="sxs-lookup"><span data-stu-id="7ab29-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="7ab29-114">`ValidationSummary`元件會摘要驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="7ab29-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="7ab29-115">`HandleValidSubmit`當表單成功提交 (通過驗證) 時, 就會觸發。</span><span class="sxs-lookup"><span data-stu-id="7ab29-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="7ab29-116">有一組內建的輸入元件可用來接收和驗證使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="7ab29-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="7ab29-117">當輸入變更時和送出表單時, 會進行驗證。</span><span class="sxs-lookup"><span data-stu-id="7ab29-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="7ab29-118">下表顯示可用的輸入元件。</span><span class="sxs-lookup"><span data-stu-id="7ab29-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="7ab29-119">輸入元件</span><span class="sxs-lookup"><span data-stu-id="7ab29-119">Input component</span></span> | <span data-ttu-id="7ab29-120">轉譯為&hellip;</span><span class="sxs-lookup"><span data-stu-id="7ab29-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="7ab29-121">所有的輸入元件 (包括`EditForm`) 都支援任意屬性。</span><span class="sxs-lookup"><span data-stu-id="7ab29-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="7ab29-122">任何不符合元件參數的屬性都會加入至轉譯的 HTML 專案。</span><span class="sxs-lookup"><span data-stu-id="7ab29-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="7ab29-123">輸入元件會提供預設行為, 以便在編輯和變更其 CSS 類別時進行驗證, 以反映欄位狀態。</span><span class="sxs-lookup"><span data-stu-id="7ab29-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="7ab29-124">某些元件包含有用的剖析邏輯。</span><span class="sxs-lookup"><span data-stu-id="7ab29-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="7ab29-125">例如, `InputDate`和`InputNumber`會藉由將無法剖析的值註冊為驗證錯誤來妥善處理。</span><span class="sxs-lookup"><span data-stu-id="7ab29-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="7ab29-126">可以接受 null 值的類型也支援目標欄位的 null 屬性 ( `int?`例如)。</span><span class="sxs-lookup"><span data-stu-id="7ab29-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="7ab29-127">下列`Starship`型別使用較早`ExampleModel`的屬性集和資料注釋, 來定義驗證邏輯:</span><span class="sxs-lookup"><span data-stu-id="7ab29-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="7ab29-128">在上述範例中, `Description`是選擇性的, 因為沒有任何資料批註存在。</span><span class="sxs-lookup"><span data-stu-id="7ab29-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="7ab29-129">下列表單會使用`Starship`模型中定義的驗證來驗證使用者輸入:</span><span class="sxs-lookup"><span data-stu-id="7ab29-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="7ab29-130">會建立做`EditContext`為[階層式值](xref:blazor/components#cascading-values-and-parameters), 以追蹤編輯程式的相關中繼資料, 包括已修改的欄位和目前的驗證訊息。`EditForm`</span><span class="sxs-lookup"><span data-stu-id="7ab29-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="7ab29-131">也提供有效和無效提交的便利事件 (`OnValidSubmit`、 `OnInvalidSubmit`)。 `EditForm`</span><span class="sxs-lookup"><span data-stu-id="7ab29-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="7ab29-132">或者, 使用`OnSubmit`來觸發驗證, 並使用自訂驗證程式代碼來檢查域值。</span><span class="sxs-lookup"><span data-stu-id="7ab29-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="7ab29-133">元件會使用資料批註, 將驗證支援附加至`EditContext`串聯的。 `DataAnnotationsValidator`</span><span class="sxs-lookup"><span data-stu-id="7ab29-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="7ab29-134">目前需要使用資料批註來啟用驗證的支援, 但我們正考慮將它設為您可以覆寫的預設行為。</span><span class="sxs-lookup"><span data-stu-id="7ab29-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="7ab29-135">若要使用不同于資料批註的驗證系統, 請`DataAnnotationsValidator`將取代為自訂的執行。</span><span class="sxs-lookup"><span data-stu-id="7ab29-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="7ab29-136">ASP.NET Core 的執行可用於參考來源中的檢查:[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="7ab29-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="7ab29-137">*ASP.NET Core 的執行會在預覽版本期間受到快速更新的需要。*</span><span class="sxs-lookup"><span data-stu-id="7ab29-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="7ab29-138">此`ValidationSummary`元件會摘要所有驗證訊息, 類似于[驗證摘要](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)標籤協助程式。</span><span class="sxs-lookup"><span data-stu-id="7ab29-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="7ab29-139">元件會顯示特定欄位的驗證訊息, 類似于[驗證訊息](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)標籤協助程式。 `ValidationMessage`</span><span class="sxs-lookup"><span data-stu-id="7ab29-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="7ab29-140">使用`For`屬性指定驗證欄位, 並以 lambda 運算式命名模型屬性:</span><span class="sxs-lookup"><span data-stu-id="7ab29-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="7ab29-141">`ValidationMessage` 和`ValidationSummary`元件支援任意屬性。</span><span class="sxs-lookup"><span data-stu-id="7ab29-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="7ab29-142">任何不符合元件參數的屬性都會加入至產生`<div>`的或`<ul>`元素。</span><span class="sxs-lookup"><span data-stu-id="7ab29-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>