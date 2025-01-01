# Selenium Quick Lookup Repository

Welcome to the Selenium Quick Lookup Repository! This repository is designed to help Automation Test Engineers and candidates by providing a quick reference to Selenium concepts, commands, design patterns, error handling, and best practices.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Architecture](#architecture)
3. [Daily Use Commands](#daily-use-commands)
4. [Cheat Sheet](#cheat-sheet)
5. [Design Patterns](#design-patterns)
6. [Error Handling](#error-handling)
7. [Best Practices](#best-practices)
8. [Contributing](#contributing)

---

## Introduction
Selenium is a powerful tool for automating web browsers. This repository serves as a quick reference guide to help users:

- Execute daily tasks efficiently.
- Understand Selenium's architecture.
- Apply advanced design patterns.
- Handle errors effectively.
- Prepare for interviews and real-world challenges.

---

## Architecture

Selenium follows a client-server architecture. Here’s a breakdown:

- **Client Libraries:** Provide bindings for various programming languages (e.g., Java, Python, C#).
- **JSON Wire Protocol:** Acts as a bridge for communication between the client and server.
- **Selenium WebDriver:** Automates browser actions using native browser APIs.
- **Browsers:** Supported browsers include Chrome, Firefox, Edge, Safari, and others.

### Diagram
```plaintext
Client Code -> Selenium WebDriver -> JSON Wire Protocol -> Browser Drivers -> Browsers
```

---

## Daily Use Commands
Here are commonly used Selenium commands categorized for quick reference:

### Browser Commands
```java
WebDriver driver = new ChromeDriver();
driver.get("https://example.com"); // Open URL
driver.quit(); // Close browser and end session
driver.close(); // Close current browser window
```

### Locators
```java
driver.findElement(By.id("elementId"));
driver.findElement(By.name("elementName"));
driver.findElement(By.xpath("//tag[@attribute='value']"));
driver.findElement(By.cssSelector("#elementId"));
driver.findElements(By.tagName("tagName"));
```

### Actions
```java
driver.findElement(By.id("button")).click(); // Click action
driver.findElement(By.id("textBox")).sendKeys("Input text"); // Enter text
String text = driver.findElement(By.id("label")).getText(); // Get text
```

### Waits
```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("elementId")));
```

---

## Cheat Sheet
### Locators Cheat Sheet
| Locator Type      | Example                                 |
|-------------------|-----------------------------------------|
| ID               | By.id("elementId")                     |
| Name             | By.name("elementName")                 |
| XPath            | By.xpath("//tag[@attribute='value']")  |
| CSS Selector     | By.cssSelector("#elementId")           |
| Tag Name         | By.tagName("tagName")                  |
| Class Name       | By.className("className")              |

### Browser Commands Cheat Sheet
| Command          | Description                     |
|-------------------|---------------------------------|
| driver.get(url)  | Open a URL                     |
| driver.close()   | Close the current window       |
| driver.quit()    | Quit the driver session        |

### Waits Cheat Sheet
| Wait Type         | Example                                    |
|-------------------|--------------------------------------------|
| Implicit Wait    | driver.manage().timeouts().implicitlyWait()|
| Explicit Wait    | WebDriverWait + ExpectedConditions         |

---

## Design Patterns
### Page Object Model (POM)
Encourages modular and maintainable test automation by separating page-specific logic.

#### Example:
```java
public class LoginPage {
    WebDriver driver;

    By username = By.id("username");
    By password = By.id("password");
    By loginButton = By.id("loginButton");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void login(String user, String pass) {
        driver.findElement(username).sendKeys(user);
        driver.findElement(password).sendKeys(pass);
        driver.findElement(loginButton).click();
    }
}
```

### Singleton Pattern
Ensures a single instance of WebDriver.

#### Example:
```java
public class WebDriverSingleton {
    private static WebDriver driver;

    private WebDriverSingleton() {}

    public static WebDriver getDriver() {
        if (driver == null) {
            driver = new ChromeDriver();
        }
        return driver;
    }
}
```

---

## Error Handling
### Common Exceptions
1. **NoSuchElementException:** Locator is incorrect or element is not present.
2. **StaleElementReferenceException:** Element is no longer attached to the DOM.
3. **TimeoutException:** Element did not load within the specified wait time.

### Handling Strategies
- Use **Explicit Waits** for dynamic elements.
- Validate element existence before interacting.
- Catch exceptions to prevent abrupt failures.

#### Example:
```java
try {
    WebElement element = driver.findElement(By.id("elementId"));
    element.click();
} catch (NoSuchElementException e) {
    System.out.println("Element not found: " + e.getMessage());
}
```

---

## Best Practices
1. Use explicit waits instead of thread.sleep().
2. Follow the Page Object Model for scalability.
3. Avoid hardcoding locators; use a centralized locator file.
4. Close or quit WebDriver sessions to release resources.
5. Write meaningful test case names and assertions.

---

## Contributing
We welcome contributions! Feel free to submit pull requests, report issues, or suggest improvements.

1. Fork the repository.
2. Create a feature branch.
3. Submit a pull request with your changes.

---

Happy Testing! 🚀
