# Ch1 - Our First Project
## Notes from the official Tricentis [documentation](https://docs.specflow.org/projects/specflow/en/latest/).

## Contents
Return [Home](README.md)
* [1.1 - Creating the System Under Test (SUT)](#01.1)
* [1.2 - Creating a SpecFlow project](#01.2)
* [1.3 - Project Reference & Running Tests](#01.3)
* [1.4 - Automating the First Scenario](#01.4)
* [1.5 - Completing first Scenario](#01.5)
* [1.6 - Fixing the Implementation](#01.6)
* [1.7 - Living Documentation](#01.7)
* [1.10 - Question & Answer](#01.10)
The code for this section can be found in ``code/Chapter01``

---
<a name="01.1"></a>
### 1.1 - Creating the System Under Test (SUT)  
* We will make a calculator project, which we will then test.
* Create a new project:
    1. Click on "Create new project" in start-up dialog
    2. Choose the template as:``Class Library``
    3. Project name: ``SpecFlowCalculator``
    4. Location: ``..\code``
    5. Solution name: ``SpecFlowCalculator``
    6. Choose the ``.NET 5 (Current)`` framework (if this is not present in the dropdown you must install it as ``individual components``).

* We will now add the following code to create the calculator.
    1. Rename ``Class1.cs`` to ``Calculator.cs``.
    2. Insert the following code:
    ```C#
    using System;

    namespace SpecFlowCalculator
    {
        public class Calculator
        {
            public int FirstNumber { get; set; }
            public int SecondNumber { get; set; }

            public int Add()
            {
                throw new NotImplementedException();
            }
        }
    }
    ```
    3. Now press ``Build Solution``.
    4. You should see this message: ``========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
    `` in the output.
* The code above would throw a ``NotImplementedException()`` object, if ``Add`` is called. You can test this by:
    ```C#
    Calculator calc = new Calculator();
    int a = calc.Add();
    ```
---
<a name="01.2"></a>
### 1.2 - Creating a SpecFlow project
* Create a SpecFLow project as following:
    1. Right click on the ``Solution 'SpecFlowCalculator'`` in the ``Solution Explorer``
    2. Go to **Add | New Project...**
    3. Add a ``SpecFlow Project``
    4. Name the project as ``SpecFlowCalculator.Specs`` in ``../code``
    5. Set the framework as ``.NET 6.0`` and test framework as ``xUnit``
    6. The project is now created with the NuGet packages.

---
<a name="01.3"></a>
### 1.3 - Project Reference & Running Tests 
* We want to test the "Calculator" class in the "SpecFlowCalculator.Specs" project.
* Therefore, we need to add a project reference to "SpecFlowCalculator" class library in the newly created SpecFlow project:
    1. Expand ``SpecFlowCalculator.Specs`` in the ``Solution Explorer``.
    2. Right-click on ``Dependencies`` and press ``Add Project Reference...``.
    3. In the reference manager, add ``SpecFlowCalculator``
    4. Build the solution
* There should be a test already added to the SpecFlow project by the template:
    1. Navigate to **Test | Test Explorer**
    2. Run ``All Tests in View`` in the top right corner.
    3. The test should fail.

---
<a name="01.4"></a>
### 1.4 - Creating First Step for a Scenario

* Open ``Calculator.feature`` and the following Gherkin scenario to will already be on file:
    ```Gherkin
    Scenario: Add two numbers
        Given the first number is 50
        And the second number is 70
        When the two numbers are added
        Then the result should be 120
    ```
* The feature file documents the expected behaviour of our calculator in a way that is human-readable.
* However, as it is, the steps of the scenario are not defined.
* Let's define the steps of the scenario:
    1. Highlight the step definition ``Given the first number is 50``, and press ``Go to Definition``, to be taken to ``CalculatorStepDefinitions`` binding.
    2. It will jump to the ``GivenTheFirstNumberIs`` method.
    3. We need to first instantiate the calculator object that we want to test:
    ```C#
    private readonly Calculator _calculator = new Calculator();
    ```
    4. Replace the ``_scenarioContext.Pending()`` or the ``throw new PendingStepException();`` with the following. This sets the first number of the calculator.
    ```C#
    [Given("the first number is (.*)")]
    public void GivenTheFirstNumberIs(int number)
    {
        _calculator.FirstNumber = number;
    }
    ```
    5. ``Build`` and then go to ``Test Explorer`` and run all tests.
    6. Press on the ``add two number`` test, and a ``Test Details Summary`` should pop up, with the following standard output:
        ```
    Given the first number is 50
    -> done: CalculatorStepDefinitions.GivenTheFirstNumberIs(50) (0.0s)
    And the second number is 70
    -> pending: CalculatorStepDefinitions.GivenTheSecondNumberIs(70)
    When the two numbers are added
    -> skipped because of previous errors
    Then the result should be 120
    -> skipped because of previous errors
        ```
* Since we have defined step definition for ``GivenTheFirstNumberIs(int number)``, in the standard output we get a ``done`` status. The remaining steps are still undefined, therefore marked with ``pending`` status.
* ``done`` means the step executed successfully with no errors.


---
<a name="01.5"></a>
### 1.5 - Completing first Scenario
* We will carry on with the step definitions:
    ```C#
    using FluentAssertions;

    namespace SpecFlowCalculator.Specs.StepDefinitions
    {
        [Binding]
        public sealed class CalculatorStepDefinitions
        {
            // For additional details on SpecFlow step definitions see https://go.specflow.org/doc-stepdef

            // **added**
            private readonly Calculator _calculator = new Calculator();

            // **added**
            private int _result;

            [Given("the first number is (.*)")]
            public void GivenTheFirstNumberIs(int number)
            {
                // Store the number in the variable FirstNumber we created in Calculator class.
                _calculator.FirstNumber = number;
            }

            [Given("the second number is (.*)")]
            public void GivenTheSecondNumberIs(int number)
            {
                // Store the number in the variable SecondNumber we created in Calculator class.
                _calculator.SecondNumber = number;

            }

            [When("the two numbers are added")]
            public void WhenTheTwoNumbersAreAdded()
            {
                //implement act (action) logic
                _result = _calculator.Add();

            }

            [Then("the result should be (.*)")]
            public void ThenTheResultShouldBe(int result)
            {
                //implement assert (verification) logic
                _result.Should().Be(result);
            }
        }
    }
    ```
* We used a variable called ``_result`` to store the results from the SUT (System Under Test).
* In order to use ``_result.Should().Be(result);``, you need to add ``using FluentAssertions;``.
* The ``then`` step checks if the result from the scenario (i.e 120) matches the output of the SUT.
* Go to **Test Explorer**, run the test and you should get this output:
    ```
    Given the first number is 50
    -> done: CalculatorStepDefinitions.GivenTheFirstNumberIs(50) (0.0s)
    And the second number is 70
    -> done: CalculatorStepDefinitions.GivenTheSecondNumberIs(70) (0.0s)
    When the two numbers are added
    -> error: The method or operation is not implemented. (0.0s)
    Then the result should be 120
    -> skipped because of previous errors
    ```
* The test fails as expected (the method is not implemented). Let's fix up the errors now.

---
<a name="01.6"></a>
### 1.6 - Fixing the Implementation
* Let's go back to our ``Calculator.cs`` application, and correct the issue:
    ```C#
    using System;

    namespace SpecFlowCalculator
    {
        public class Calculator
        {
            public int FirstNumber { get; set; }
            public int SecondNumber { get; set; }

            public int Add()
            {
                return FirstNumber + SecondNumber;
            }
        }
    }
    ```
* Build and then run the test. 
* Standard Output:
    ```
        Given the first number is 50
    -> done: CalculatorStepDefinitions.GivenTheFirstNumberIs(50) (0.0s)
    And the second number is 70
    -> done: CalculatorStepDefinitions.GivenTheSecondNumberIs(70) (0.0s)
    When the two numbers are added
    -> done: CalculatorStepDefinitions.WhenTheTwoNumbersAreAdded() (0.0s)
    Then the result should be 120
    -> done: CalculatorStepDefinitions.ThenTheResultShouldBe(120) (0.0s)
    ```
* Tests have now passed.

> Check out the code folder to see how a subtract scenario is implemented.

---
<a name="01.7"></a>
### 1.7 - Living Documentation
1. Go to command prompt and type: ``dotnet tool install --global SpecFlow.Plus.LivingDoc.CLI``
2. Navigate to the output directory of the SpecFlow project: ``cd ...\code\SpecFlowCalculator\SpecFlowCalculator.Specs\bin\Debug\netnet6.0``
3. Run: ``livingdoc test-assembly SpecFlowCalculator.Specs.dll -t TestExecution.json``
4. Open the ``LivingDoc.html`` file which will be stored in the output directory from step 2.
5. Explore your test.

<a name="01.10"></a>
### 1.10 - Question & Answer
<br>

<details>
<summary><b>

1. What are the differences between the different test frameworks (xUnit, NUnit, etc.)?</b></summary>
<br>
* These are both unit test frameworks
* NUnit was developed for the .NET framework ported from JUnit.
* xUnit is a more recent Unit Testing Framework
* The main difference between the two is that NUnit will run all the tests using the same class instance, while xUnit will create a new instance for each test.
<br><br></details>

<details>
<summary><b>

2. What is NuGet?</b></summary>
<br>
* NuGet is the package manager for .NET.
* You can see the NuGet packages in **Dependencies | Packages** in the Solutions Explorer.
<br><br></details>