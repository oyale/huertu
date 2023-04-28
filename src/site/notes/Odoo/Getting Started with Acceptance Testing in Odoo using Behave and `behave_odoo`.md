---
{"dg-publish":true,"topics":"testing, odoo, behave, selenium","permalink":"/odoo/getting-started-with-acceptance-testing-in-odoo-using-behave-and-behave-odoo/","dgPassFrontmatter":true}
---

As a side project, I recently developed an (MVP) Odoo 14 module called [odoo\_instance](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/tree/14.0/odoo_instance), which centralizes information about deployed Odoo instances. This experience led me to revisit an old pending task: familiarizing myself with acceptance testing. 
&NewLine;
Along the way, I created the `behave_odoo` package, which was born out of my need for a more efficient testing process. In this article, I'll share my journey and discuss how to implement acceptance testing effectively for Odoo modules using the `behave` framework, the web-automation package `selenium`  and the `behave_odoo` package.

### A quick glance at the types of tests
&NewLine;
There are several ways to categorize tests. For instance, there are **unit tests**, which test the functionality of each code unit isolated. To perform these checks, all functionalities that are necessary but not present in the code being tested are simulated. 
&NewLine;
For example, if the code needs to consume an external API, the API's response will be simulated instead of making the actual call. To further clarify, these tests are not trying to verify if the API works, but rather, given a correct response from the API, if the code does what it's supposed to do.
&NewLine;
At the other end of this spectrum, we find **acceptance tests**, which check functionality from the end-user's perspective, without considering the details of implementation. This type of test is closely related to Test-Driven Development, more specifically, to [Behaviour-Driven Development](https://behave.readthedocs.io/en/stable/philosophy.html), although they are not exclusive to this methodology.
&NewLine;
This methodology proposes a workflow in which **requirements are gathered in natural language** (better said, in a subset of natural language called Gherkins). After that, the code performing the tests is implemented, and only as last step, the code itself is developed.
&NewLine;
Even without fully embracing Behavior-Driven Development (BDD), acceptance tests can still be incredibly useful in ensuring that your software meets the expectations and requirements of its end-users. **By simulating real-world scenarios and user interactions, acceptance tests provide valuable insights into the overall user experience and functionality of your application**. These tests can help identify issues that may not be apparent through unit testing or other testing methods, allowing your development team to address problems before they become more significant
&NewLine;
#### Hands on

We will implement a basic acceptance test for the new Odoo module. Even though it was not developed following the BDD methodology, we will implement acceptance tests using the `behave` framework. When dealing with a web application, as in our case, we need an additional piece that allows us to interact automatically with the browser. [`selenium`](https://www.selenium.dev/) is the automation software we will use for this purpose.
&NewLine;
#### Writing the requirements

The [Gherkin](https://cucumber.io/docs/gherkin/reference/) language uses a series of reserved words to structure the requirements of the tests, called `features`. For each step, it uses the keywords Given, When, Then, And, But.
&NewLine;
Let's see an example of a `feature` applied to our module:
&NewLine;
```gherkin
Feature: Instance management

  Scenario: Create and delete an instance
    Given the user is on the Odoo Instance form view
    When the user fills in the form with valid data
    And the user clicks on the "Save" button
    Then a new instance should be created
    When the user clicks on the "Delete" button
    Then the instance should be deleted>
```
&NewLine;
#### Configuring behave

To install behave, we can simply use `pip install behave`. We need to also install `selenium` and a (Chrom* or Firefox) webdriver.  
&NewLine;
We will create, at the root of the module, the `features` folder with the following content:

.
├── features
│   ├── behave.ini
│   ├── environment.py
│   ├── instance_management.feature
│   └── steps
│       ├── instance_management_steps.py
&NewLine;
- [`behave.ini`](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/raw/14.0/odoo_instance/features/behave.ini) is the basic configuration file of behave. In it, we will also declare the variables that we want to use in the tests.
- [`environment.py`](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/raw/14.0/odoo_instance/features/behave.ini) is responsible for setting up the environment in which the tests will be developed. Here we can load the variables defined in the `.ini` file, prepare the browser, etc.
- `instance_management.feature` is the Gherkin file we saw in the previous section.
- `steps/instance_management_steps.py` is the file that implements the logic of the tests.
&NewLine;
### How to implement the steps

The real meat comes when we must implement the code for each step declared in the `.feature` file.
&NewLine;
The format is as follows:
&NewLine;
```python
@keyword("text in the requirements declaration")
def step_impl(context):
    ...logic for each step
```
&NewLine;
`Selenium` provides a [series of functions](https://www.selenium.dev/documentation/webdriver/) that allow us to interact with the browser's content. In a highly simplified manner, the workflow I followed involved finding an element on the web page using a CSS or XPath selector and interacting with it through Selenium's functionalities. For example:
&NewLine;
```
    instance_type_select = WebDriverWait(context.browser, 20).until(
        EC.element_to_be_clickable((By.XPATH, "//select[@name='instance_type']"))
    )
    
    Select(instance_type_select).select_by_visible_text("Test")
```
&NewLine;
These two lines of code wait for the element (dropdown) with `instance_type` in its `name` attribute to become clickable, and when it does, the "Test" option is selected.
&NewLine;
Getting this part to work well has been the most tedious aspect of the whole process for me (my bad: I haven't tried Selenium IDE, which seems to greatly improve the user experience). But once the research is done, and considering that Odoo is a framework... Why not create a series of specific functions to interact with Odoo?
&NewLine;
### Introducing `behave_odoo`.

[`behave_odoo`](https://github.com/coopdevs/behave_odoo) is nothing more than a package of helper functions for developing tests for Odoo with Selenium. It compiles methods to write tests more quickly. For the previous case, we have this function:
&NewLine;
```
set_select_field(context, select_item, option)
```
&NewLine;
This function will save us from having to search for the XPath and the correct way to interact when we want to interact with a selection field.
&NewLine;
The helpers functions are fully documented on package's site: [https://coopdevs.github.io/behave\_odoo/](https://coopdevs.github.io/behave_odoo/)
&NewLine;
Thanks to the use of `behave_odoo`, we have been able to reduce the implementation of tests for the described Scenario from [131 horrendous lines](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/blob/f3f61384c9616cb49f1fa6b549bf11e84377b817/odoo_instance/features/steps/instance_management_steps.py) to [55 clean and easy-to-read](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/blob/6a5186fb293feb2f05e1a6a9af2b03b59f242a69/odoo_instance/features/steps/instance_management_steps.py) ones. Moreover, the creation of new tests now seems like a much more manageable task.
&NewLine;
## Last words

In conclusion, adopting the `behave` framework for acceptance testing and leveraging the `behave_odoo` package to simplify interactions with Odoo has significantly improved the testing process. By reducing the lines of code and making them more readable, we've increased the maintainability of the tests and made it easier to create new tests in the future.
&NewLine;
To ensure a smooth experience when implementing acceptance tests in Odoo, here are some final tips:
&NewLine;
1. Invest some time in learning Selenium and its functionalities. This will help you better understand how to interact with the browser and improve the efficiency of your tests.

2. Keep your tests modular and maintainable by using helper functions and packages such as `behave_odoo`. This will save you time in the long run and make your tests more readable for others who may work on the project.

3. Don't be afraid to iterate and improve your testing process over time. As you gain experience, you'll be able to identify areas for improvement and make the necessary adjustments to optimize your workflow.
&NewLine;

By following these tips and continuously refining your testing approach, you'll be able to create robust and effective acceptance tests for your Odoo modules, ensuring that they meet the needs of your end-users and maintain a high level of quality throughout their development.
&NewLine;
### Show me the code
If you're interested in exploring the code for both the `odoo_instance` module and the tests I've described in this article, you can find them at the following repository: [https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/tree/14.0/odoo_instance](https://git.coopdevs.org/coopdevs/odoo/odoo-addons/odoo14-addons/-/tree/14.0/odoo_instance). 
&NewLine;
Happy testing!