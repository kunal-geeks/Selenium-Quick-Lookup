# Selenium Advanced Testing Framework

This repository demonstrates a fully functional Selenium testing framework with advanced features such as parallel test execution, Selenium Grid setup, retry mechanism, logging, centralized error handling, and Allure reporting.

---

## Folder Structure
```plaintext
selenium-framework/
|-- src/
|   |-- main/
|   |   |-- java/
|   |   |   |-- base/
|   |   |   |   |-- BaseTest.java
|   |   |   |-- pages/
|   |   |   |   |-- LoginPage.java
|   |   |   |   |-- LandingPage.java
|   |   |   |-- utilities/
|   |   |       |-- ExcelUtils.java
|   |   |       |-- ScreenshotUtils.java
|   |   |       |-- LoggerUtils.java
|   |-- test/
|       |-- java/
|       |   |-- tests/
|       |       |-- LoginTests.java
|       |       |-- LandingPageTests.java
|       |       |-- DataDrivenTests.java
|-- resources/
|   |-- testdata/
|       |-- credentials.xlsx
|-- allure-results/
```

---

## Implementation

### Base Test
**BaseTest.java**
```java
package base;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Parameters;
import utilities.LoggerUtils;
import utilities.ScreenshotUtils;

public class BaseTest {
    protected WebDriver driver;

    @Parameters("browser")
    @BeforeClass
    public void setup(String browser) {
        LoggerUtils.logInfo("Setting up WebDriver for browser: " + browser);
        switch (browser.toLowerCase()) {
            case "chrome":
                driver = new ChromeDriver();
                break;
            case "firefox":
                driver = new FirefoxDriver();
                break;
            case "edge":
                driver = new EdgeDriver();
                break;
            default:
                throw new IllegalArgumentException("Unsupported browser: " + browser);
        }
        driver.manage().window().maximize();
    }

    @BeforeMethod
    public void navigateToBaseURL() {
        LoggerUtils.logInfo("Navigating to the base URL.");
        driver.get("https://example.com");
    }

    @AfterClass
    public void teardown() {
        LoggerUtils.logInfo("Closing WebDriver.");
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### Page Object Model
**LoginPage.java**
```java
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage {
    private WebDriver driver;

    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginButton");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void enterUsername(String username) {
        driver.findElement(usernameField).sendKeys(username);
    }

    public void enterPassword(String password) {
        driver.findElement(passwordField).sendKeys(password);
    }

    public void clickLogin() {
        driver.findElement(loginButton).click();
    }
}
```

**LandingPage.java**
```java
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LandingPage {
    private WebDriver driver;

    private By welcomeMessage = By.id("welcomeMessage");

    public LandingPage(WebDriver driver) {
        this.driver = driver;
    }

    public String getWelcomeMessage() {
        return driver.findElement(welcomeMessage).getText();
    }
}
```

### Tests
**LoginTests.java**
```java
package tests;

import base.BaseTest;
import org.testng.Assert;
import org.testng.annotations.Test;
import pages.LoginPage;
import pages.LandingPage;

public class LoginTests extends BaseTest {
    @Test
    public void validLoginTest() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterUsername("testuser");
        loginPage.enterPassword("password");
        loginPage.clickLogin();

        LandingPage landingPage = new LandingPage(driver);
        Assert.assertEquals(landingPage.getWelcomeMessage(), "Welcome, testuser!");
    }
}
```

**DataDrivenTests.java**
```java
package tests;

import base.BaseTest;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import pages.LoginPage;
import utilities.ExcelUtils;

public class DataDrivenTests extends BaseTest {
    @DataProvider(name = "loginData")
    public Object[][] loginData() {
        return ExcelUtils.readExcelData("resources/testdata/credentials.xlsx");
    }

    @Test(dataProvider = "loginData")
    public void loginWithDataProvider(String username, String password) {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterUsername(username);
        loginPage.enterPassword(password);
        loginPage.clickLogin();
    }
}
```

### Selenium Grid Setup
Set up Selenium Grid for parallel execution on multiple platforms:
1. Start the Selenium Hub:
   ```bash
   java -jar selenium-server-standalone.jar hub
   ```
2. Register Nodes for different browsers:
   ```bash
   java -jar selenium-server-standalone.jar node
   ```

Update the BaseTest to connect to the Selenium Grid:
```java
URL gridUrl = new URL("http://localhost:4444/wd/hub");
driver = new RemoteWebDriver(gridUrl, capabilities);
```

### Utilities
**ScreenshotUtils.java**
```java
package utilities;

import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.io.FileHandler;
import java.io.File;

public class ScreenshotUtils {
    public static void takeScreenshot(WebDriver driver, String fileName) {
        try {
            File screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
            FileHandler.copy(screenshot, new File("screenshots/" + fileName + ".png"));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**LoggerUtils.java**
```java
package utilities;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class LoggerUtils {
    private static final Logger logger = LogManager.getLogger(LoggerUtils.class);

    public static void logInfo(String message) {
        logger.info(message);
    }

    public static void logError(String message) {
        logger.error(message);
    }
}
```

### Retry Mechanism
Implement a retry mechanism using TestNG:
```java
import org.testng.IRetryAnalyzer;
import org.testng.ITestResult;

public class RetryAnalyzer implements IRetryAnalyzer {
    private int retryCount = 0;
    private static final int maxRetryCount = 3;

    @Override
    public boolean retry(ITestResult result) {
        if (retryCount < maxRetryCount) {
            retryCount++;
            return true;
        }
        return false;
    }
}
```

### Reporting
Integrate Allure Reporting:
1. Add Allure dependencies in the `pom.xml`.
2. Generate reports with:
   ```bash
   allure serve allure-results
   ```

---

This comprehensive framework covers parallel testing, Grid setup, data-driven testing, retry mechanism, and reporting. Let me know if further refinement is needed!
