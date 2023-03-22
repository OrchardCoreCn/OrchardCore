# 内容本地化 (`OrchardCore.ContentLocalization`)

该模块允许您本地化内容项。

## LocalizationPart

将此部分附加到内容类型以管理多个本地化版本的内容项。

## ContentCulturePicker (`OrchardCore.ContentLocalization.ContentCulturePicker`)

## ContentCulturePicker 功能

`ContentCulturePicker` 功能可帮助您管理前端的文化。

启用此模块会导致

- 将 `ContentRequestCultureProvider` 添加为用于确定当前线程文化的第一个方法。
    此提供程序将根据与当前 URL 匹配的 ContentItem 设置线程文化。
- 前端主题提供了 2 个形状（如下所述）。

`ContentCulturePicker` 使用以下规则选择要重定向的 URL

- 如果 `ContentItem` 有一个相关的 ContentItem 用于所选文化，则重定向到该项。
- 或者，如果指定了 HomePage，则尝试查找当前文化的 Homepage `ContentItem` 的本地化。
- 或者重定向到当前页面。

### 本地化 Cookie

默认情况下，`ContentCulturePicker` 为 `CookieRequestCultureProvider` 设置了一个 cookie。可以在 `Configuration/Settings/Localization/Content Culture Picker` 设置页面中禁用此功能。

`ContentRequestCultureProvider` 可以根据与当前 URL 匹配的 ContentItem 设置 cookie。可以在 `Configuration/Settings/Localization/Content Request Culture Provider` 设置页面中编辑此设置。


#### 配方步骤

可以使用设置步骤在配方期间设置 cookie。以下是一个示例步骤：

```json
{
  "name": "settings",
  "ContentCulturePickerSettings": {
    "SetCookie": true
  },
  "ContentRequestCultureProvider": {
      "SetCookie": true
  }
},
```

### 形状

#### `ContentCulturePicker`

`ContentCulturePicker` 形状为 `ContentCulturePickerContainer` 形状加载数据。  
您应始终在主题中呈现此形状：

=== "Liquid"

    ``` liquid
    {% shape "ContentCulturePicker" %}
    ```

=== "Razor"

    ``` html
    <shape type="ContentCulturePicker" />
    ```

#### `ContentCulturePickerContainer`

`ContentCulturePickerContainer` 形状用于呈现 `ContentCulturePicker`。
您应在主题中覆盖此形状。

| 属性                  | 描述                                                 |
| ------------------------- | ----------------------------------------------------------- |
| `Model.CurrentCulture`    | CultureViewModel {Name, DisplayName} 表示当前线程文化。 |
| `Model.SupportedCultures` | 所有支持的文化的 CultureViewModel 对象列表。   |

##### ContentCulturePickerContainer 示例

=== "Liquid"

    ``` liquid
    <ul>
        <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="oc-culture-picker" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">{{Model.CurrentCulture.DisplayName}}</a>
            <div class="dropdown-menu" aria-labelledby="oc-culture-picker">
            {% for culture in Model.SupportedCultures %}
                {% if culture.Name != Model.CurrentCulture.Name  %}
                <a class="dropdown-item" href="{{culture.Name | switch_culture_url }}">{{culture.DisplayName}}</a>
                {% endif %}
            {% endfor %}
            </div>
        </li>
    </ul>
    ```

=== "Razor"

    ``` html
    <ul>
        <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="oc-culture-picker" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">@Model.CurrentCulture.DisplayName</a>
            <div class="dropdown-menu" aria-labelledby="oc-culture-picker">
                @foreach (var culture in Model.SupportedCultures)
                {
                    if (!string.Equals((string)culture.Name, (string)Model.CurrentCulture.Name, StringComparison.OrdinalIgnoreCase))
                    {
                        <a asp-route="RedirectToLocalizedContent"
                        asp-route-area="OrchardCore.ContentLocalization"
                        asp-route-targetculture="@culture.Name"
                        asp-route-contentItemUrl="@Context.Request.Path"
                        class="dropdown-item">@culture.DisplayName</a>
                    }
                }
            </div>
        </li>
    </ul>
    ```

## Liquid 过滤器

### `switch_culture_url`

返回切换文化的操作的 URL。

输入

```liquid
{{ Model.Culture.Name | switch_culture_url }}
```

输出

```text
/Loc1/RedirectToLocalizedContent?targetculture=fr&contentItemUrl=%2Fblog
```

### `localization_set`

返回指定文化（默认为请求文化）中的内容项。

输入

```liquid
{{ Model.ContentItem.Content.LocalizationPart.LocalizationSet | localization_set: "en" }}
```

输出

```text
Title
```

## 配置

默认情况下使用以下配置，可以自定义：

```json
{
   "OrchardCore": {
    "OrchardCore_ContentLocalization_CulturePickerOptions": {
     "CookieLifeTime": 14 // 设置文化选择器 cookie 的生存期（以天为单位）。
    }
  }
}
```

> 该文档由ChatGPT 4 翻译
