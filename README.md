<br />
<div align="center">
  <h1 align="center">
    <img src="pics/rpai-icon.png" alt="Logo" width="300" height="80">
  </h1>

  <p align="center">
    Internal component for utilizing AI capabilities in selenium automation.
    <br />
  </p>
  <p align="center">Updated on: 4.10.2024 Daniel Nikkari</p>
  <p align="center">Created on: 7.4.2024 Daniel Nikkari</p>
  <p align="center">
    <br />
    <a href="https://azure.microsoft.com/en-us/pricing/details/cognitive-services/openai-service/">Azure OpenAI API pricing</a>
  </p>
</div>

<!--AI models in use-->
## 🔮 AI models

`Recommended model:`

![GPT Icon][GPT4o-mini]

| Input price per 1000 tokens (€)  | Output price per 1000 tokens (€) |
| ------------- | ------------- |
| 0.00014  | 0.0006  |

> [!NOTE]
> `RPAI` supports models from GPT4 and upwards.

<!--RPAI object methods-->
## 🌍 RPAI usage


### 🔨 Initiating RPAI

> [!TIP]
> To initiate the `RPAI` componenet you will need a webdriver (e.g. if you have a SeleniunSession initated just pass session.driver), API key to the Azure OpenAI, endpoint of the Azure OpenAI deployment, API version (can be found from Azure), and deployment, which is the name of the specific AI model deployment in Azure (e.g. DigiaInternal-GPT-4o-mini).

> [!TIP]
> If you want to change the creatity of the model, you may pass temperature value when you initiate `RPAI`, accepted values range from 0.0 to 2.0, higher values like 0.8 will make the output more random and creative, lower values like 0.2 will make the output more predictable and repetitive, default value is 0.3.

```Python
>>> from rpai import RPAI
>>> ai = RPAI(
      driver: WebDriver,
      key: str,
      endpoint: str,
      api_version: str,
      deployment: str,
      temperature: float = 0.3,
      token_threshold: int = 128000,
      automatic_context: bool = True,
      use_database: bool = True,
      map_size: int = 1e9,
    ) -> RPAI
```
- `token_threshold`: Max context window with the model
- `automatic_context`: Automatically decide what context is required to fulfil the user prompt
- `use_database`: Use Lightning Memory-Mapped Database (LMDB) to store found XPaths and update the path only if it is not valid anymore
- `map_size`: Max memory size of LMDB (defaulting to 1 GB if not given)

### ☝️ Clicking an element

```Python
>>> ai.click(
      prompt: str,
      max_wait: int = 10,
      show_element: bool = False,
      svg_icon_information: bool = None,
      semantic_information: bool = None,
      color_information: bool = None,
    ) -> WebElement
```

- `show_element`: If set True a border is drawn around the element that the AI interacts with for improved transparency
- `svg_icon_information`: If set True, you may refer to an SVG icon in your prompt, for example, *"Find the element with shopping cart icon"*
- `semantic_information`: If set True, you may refer to the location of the element in the viewport, for example, *Find the search icon in upper-right corner*
- `color_information`: If set True elements text color, background color, and border color information will be included

### 🔑 Sending input

```Python
>>> ai.send_keys(prompt: str,
      keys: str,
      key: Keys = None,
      max_wait: int = 10,
      show_element: bool = False,
      svg_icon_information: bool = None,
      semantic_information: bool = None,
      color_information: bool = None,
    ) -> WebElement
```
- `keys`: Input text to be written into editable field
- `key`: Default `None`, add if you want to, for example, press Keys.ENTER after typing the keys
- `show_element`: If set True a border is drawn around the element that the AI interacts with for improved transparency
- `svg_icon_information`: If set True, you may refer to an SVG icon in your prompt, for example, *"Find the element with shopping cart icon"*
- `semantic_information`: If set True, you may refer to the location of the element in the viewport, for example, *Find the search icon in upper-right corner*
- `color_information`: If set True elements text color, background color, and border color information will be included

### 💡 Using dropdown menu select->option

```Python
>>> ai.select(prompt: str,
      option: str,
      max_wait: int = 10,
      show_element: bool = False,
      svg_icon_information: bool = None,
      semantic_information: bool = None,
      color_information: bool = None,
    ) -> WebElement
```
- `option`: Value of the option to be chosen, uses *select by visible text*
- `show_element`: If set True a border is drawn around the element that the AI interacts with for improved transparency
- `svg_icon_information`: If set True, you may refer to an SVG icon in your prompt, for example, *"Find the element with shopping cart icon"*
- `semantic_information`: If set True, you may refer to the location of the element in the viewport, for example, *Find the search icon in upper-right corner*
- `color_information`: If set True elements text color, background color, and border color information will be included

### 🚧 Finding an element with AI
If you need to find the element itself and get it from the AI, you may use the function ``rpai.get_element(prompt: str)``
```Python
>>> ai.get_element(prompt: str,
      max_wait: int = 10,
      show_element: bool = False,
      svg_icon_information: bool = None,
      semantic_information: bool = None,
      color_information: bool = None,
    ) -> WebElement
```
- `show_element`: If set True a border is drawn around the element that the AI interacts with for improved transparency
- `svg_icon_information`: If set True, you may refer to an SVG icon in your prompt, for example, *"Find the element with shopping cart icon"*
- `semantic_information`: If set True, you may refer to the location of the element in the viewport, for example, *Find the search icon in upper-right corner*
- `color_information`: If set True elements text color, background color, and border color information will be included

> [!NOTE]
> Every method returns the found Selenium WebElement based on the user prompt.


### Get token count for the interactable elements in the current DOM

```Python
>>> ai.get_dom_elements_token_count(
      svg_icon_information:bool=False,
      semantic_information:bool=True,
      color_information:bool=True,
    ) -> int
```

## ⛳ Example use case
**CASE:** Customer wants you to open the page *Documents* from the navigation, find search field and input "XYZ Document" and search. From the search results the first result should be chosen and then from there an option "XY" should be selected from a dropdown menu.

**EXAMPLE:**
```Python
from rpai.rpai import RPAI
from selenium_session import SeleniumSession
from selenium.webdriver.common.keys import Keys

session = SeleniumSession()
rpai = RPAI(
  driver=session.driver,
  key=API_KEY,
  endpoint=API_ENDPOINT,
  api_version=OPENAI_API_VERSION,
  deployment=OPENAI_API_DEPLOYMENT,
)

session.get("https://url.com")

rpai.click("Find me the 'Documents' link from the navigation bar.")
rpai.send_keys(prompt="Find the search input field.", keys="XYZ Document", key=Keys.ENTER)
rpai.click("Find the first search result.")
rpai.select(prompt="Find the XY dropdown menu.", option="XY")
```

## 📜 Prompt tips

1. #### **Specify the correct HTML element**
    - Use Exact Tag Names: Clearly state the HTML tag of the element you want to interact with.
      - Example: If you want to click a button, specify it as a button:
        ```Python
        ai.click("Click the 'Submit' button.")
        ```
      - Note: If an element looks like a button but is actually a link (<a\> tag), referring to it as a button might confuse the AI.
    - Avoid Ambiguity: Do not rely on visual appearance alone; specify the element type.

2. #### **Provide Text or Labels**
    - Include Text Content: Use the text displayed on the element.
      ```Python
      ai.click("Find the 'Shortest Path' link.")
      ```

3. #### **Use Additional Attributes for Clarity**
    - Reference Unique Attributes: Mention attributes like id, class, or href to help the AI find the element.
      ```Python
      ai.click("Click the link with href containing 'shortestpath'.")
      ```

4. #### **Enhance Instructions When Facing Difficulties**
    - Combine Multiple Identifiers: Use both text and attributes if the AI struggles to find the element.
      ```Python
      ai.click(
        prompt="Please, provide me the element with magnifying glass in top-right corner of the viewport.",
        svg_icon_information=True,
      )
      ```

5. #### **Check AI Reasoning for Troubleshooting**
    - Review AI Response Logs: Examine the AI's reasoning or JSON output to understand its actions.
    - Adjust Based on Feedback: Modify your prompt if the AI misunderstood the instruction.


### 📝 Logging

`RPAI` will log the full XPath to the found WebElement which you may use to confirm that the correct element was found.



<!-- Markdown links & images -->
[GPT4o-mini]: /pics/openai-gpt4o-mini.svg
