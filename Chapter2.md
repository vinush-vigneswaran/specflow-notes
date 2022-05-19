# Ch2 - Selenium with Page Object Model Pattern
## Notes from the official Tricentis [documentation](https://docs.specflow.org/projects/specflow/en/latest/).

## Contents
Return [Home](README.md)
* [2.1 - Setting up the Project](#02.1)
* [2.2 - Gherkin Scenario & Configuring Chrome Drivers ](#02.2)
* [2.3 - Selenium with Page Object Model Pattern](#02.3)

The code for this section can be found in ``code/CalculatorSelenium``

---
<a name="02.1"></a>
### 2.1 - Setting up the Project
We will use a sample project to understand how Selenium can be used, and the benefit of following a Page Object Model pattern.

1. Clone the project into your ``../code`` file from this [Github repo](https://github.com/SpecFlowOSS/SpecFlow-Examples/tree/master/CalculatorSelenium). You can also find the project in this repo in ``.../code/CalculatorSelenium``. *This project requires .NET Core 3.1*
2. In ``Solution Explorer``, right-click on on ``CalculatorSelenium.Specs`` and select ``Manage NuGet Packages...``.
3. Add ``Selenium.Support`` (the main package for selenium) and ``Selenium.WebDriver.ChromeDriver`` for the Chrome Driver support required by Selenium.

---
<a name="02.2"></a>
### 2.2 - Gherkin Scenario & Configuring Chrome Drivers 

* **Gherkin Scenarios** 
    * We will add a Scenario Outline to the ``Calculator.feature``, to carry out multiple tests:
    ```
    Scenario: Add two numbers
        Given the first number is 50
        And the second number is 70
        When the two numbers are added
        Then the result should be 120


    Scenario Outline: Add two numbers permutations
        Given the first number is <First number>
        And the second number is <Second number>
        When the two numbers are added
        Then the result should be <Expected result>

    Examples:
        | First number | Second number | Expected result |
        | 0            | 0             | 0               |
        | -1           | 10            | 9               |
        | 6            | 9             | 15              |
    ```
* **Openning and Closing Chrome for Tests**
    * We will be testing the web calculator from this [link](https://specflowoss.github.io/Calculator-Demo/Calculator.html).
    * Lets create a ``BrowserDriver`` class to manage the main browser activities: starting and ending the browser:
    ```C#
    using System;
    using OpenQA.Selenium;
    using OpenQA.Selenium.Chrome;

    namespace CalculatorSelenium.Specs.Drivers
    {
        /// <summary>
        /// Manages a browser instance using Selenium
        /// </summary>
        public class BrowserDriver : IDisposable
        {
            private readonly Lazy<IWebDriver> _currentWebDriverLazy;
            private bool _isDisposed;

            public BrowserDriver()
            {
                _currentWebDriverLazy = new Lazy<IWebDriver>(CreateWebDriver);
            }

            /// <summary>
            /// The Selenium IWebDriver instance
            /// </summary>
            public IWebDriver Current => _currentWebDriverLazy.Value;

            /// <summary>
            /// Creates the Selenium web driver (opens a browser)
            /// </summary>
            /// <returns></returns>
            private IWebDriver CreateWebDriver()
            {
                //We use the Chrome browser
                var chromeDriverService = ChromeDriverService.CreateDefaultService();

                var chromeOptions = new ChromeOptions();

                var chromeDriver = new ChromeDriver(chromeDriverService, chromeOptions);

                return chromeDriver;
            }

            /// <summary>
            /// Disposes the Selenium web driver (closing the browser) after the Scenario completed
            /// </summary>
            public void Dispose()
            {
                if (_isDisposed)
                {
                    return;
                }

                if (_currentWebDriverLazy.IsValueCreated)
                {
                    Current.Quit();
                }

                _isDisposed = true;
            }
        }
    }
    ```
* **Code Explained**
    * **Interface** : An interface is a programming structure/syntax that allows the computer to enforce certain properties on an object (class). It enforces a certain function is implemented as part of the object method.
    * ``IDisposable`` : The primary use of this interface is to release unmanaged resources. The garbage collector automatically releases the memory allocated to a managed object when that object is no longer used.
    * **Type Parameter** : TypeParameter is a generic label used in generic programming to reference an unknown data type, data structure, or class.
    * Read more on **Generics** to understand the triangle brackets.
    * ``IWebDriver`` :
    * ``Lazy<IWebDriver>`` :












---
<a name="02.3"></a>
### 2.3 - Selenium with Page Object Model Pattern

* Selenium is an open-source automation framework used for web applications across different browsers and platforms.
* Below we will show how the file structure should be for a Selenium Project

* **Page Object Model (POM)** 
    * POM is a design pattern used to abstract the page into different classes.
    * With this model, you have the element selectors in dedicated locations (class files) and no scattered around in your code.
    * The advantage of the model is that it reduces code duplication and improves test maintenance.


\[Further content to be added\]







