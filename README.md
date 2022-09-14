import time
import pytest
from selenium.webdriver.common.by import By
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.support.wait import WebDriverWait
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.alert import Alert
from selenium.webdriver.common.keys import Keys

users_list = ["ofir123@gmail.com", "ofir123"], ["ofir123@walla.com", "ofir1234"], ["ofir123@yahoo.com", "ofir12345"], ["Eliyah@gmail.com", "maxmax"], ["maximnudler4@gmail.com", "max123"]

@pytest.fixture()
def setup():
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
    driver.get("https://svburger1.co.il/#/HomePage")
    driver.implicitly_wait(10)
    driver.maximize_window()
    yield driver
    driver.close()


def test_sanity(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH, '//h2[text()="SVBURGER WEATHER"]')
    driver.find_element(By.XPATH, "//*[contains(text(),'Kids')]").click()
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH, '//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys()
    time.sleep(5)
    driver.find_element(By.XPATH, '//button[text() = "Send"]').send_keys(Keys.ENTER)
    time.sleep(5)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()


@pytest.mark.parametrize("list1", users_list)
def test_signin_func1to5(setup, list1):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys(list1[0])
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys(list1[1])
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    assert driver.find_element(By.XPATH, '//h5[text()="Combo Meal"]').is_displayed()

def test_signin_eh1(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys()
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "Failed to log in" in text

def test_signin_eh2(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "Failed to log in" in text

def test_register_func1(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("Ofir")
    driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@hhhh.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("ofir123")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("ofir123")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    time.sleep(10)
    assert driver.find_element(By.XPATH, '//h5[text()="Combo Meal"]').is_displayed()

def test_register_func2(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    # driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("Ofir")
    # driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("Eliyah@gmail.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "First name must be in English letters only" in text


def test_register_func3(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("nudlerr")
    # driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("Eli@gmail.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "Last name must be in English letters only" in text


def test_register_func4(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    # driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("")
    driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("Eli@gmail.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "First name must be in English letters only" in text


def test_register_func5(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("ofir")
    driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir1224@yahoo.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("ofir12345")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("ofir12345")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    time.sleep(5)
    assert driver.find_element(By.XPATH, '//h5[text()="Combo Meal"]').is_displayed()
#
def test_register_eh1(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("ofir")
    driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("test5@gmail.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("maxmax!")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("maxmax!@")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "password and confirm error" in text
#
#
#
def test_register_eh2(setup):
    driver = setup
    driver.find_element(By.XPATH, '//a[@href="#/SignUp"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Type your first name"]').send_keys("ofir")
    driver.find_element(By.XPATH, '//input[@placeholder="Type your last name"]').send_keys("Veksler")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("maximnudler@gmail.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Create Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//input[@placeholder="Confirm Password"]').send_keys("maxmax")
    driver.find_element(By.XPATH, '//button[@type="submit"]').click()
    time.sleep(5)
    alert_window = driver.switch_to.alert
    time.sleep(3)
    text = alert_window.text
    time.sleep(3)
    alert_window.accept()
    assert "The email address is already in use by another account" in text


def test_reserv_func1(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    driver.find_element(By.XPATH, "//*[contains(text(),'Combo Meal')]").click()
    time.sleep(1)
    driver.find_element(By.XPATH, "//h5[contains(text(), 'Kids')]").click()
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys()
    driver.find_element(By.XPATH, '//button[text() = "Send"]').send_keys(Keys.ENTER)
    time.sleep(3)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()

def test_reserv_func2(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    driver.find_element(By.XPATH, "//*[contains(text(),'Combo Meal')]").click()
    time.sleep(5)
    assert driver.find_element(By.XPATH, "//*[contains(text(),'Combo Meal')]").click()

def test_reserv_func3(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    time.sleep(5)
    driver.find_element(By.XPATH, "//*[contains(text(),'Burger')]").click()
    time.sleep(5)
    fries = driver.find_element(By.XPATH, "//h5[contains(text(), 'Sides')]")
    driver.execute_script("arguments[0].scrollIntoView();", fries)
    time.sleep(5)
    fries.click()
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys()
    driver.find_element(By.XPATH, '//button[text() = "Send"]').send_keys(Keys.ENTER)
    time.sleep(3)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()

def test_reserv_func4(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    time.sleep(8)
    driver.find_element(By.XPATH, "//*[contains(text(),'Kids')]").click()
    time.sleep(5)
    test2 = driver.find_element(By.XPATH,'//*[contains(text(),"Vegan")]')
    time.sleep(8)
    driver.execute_script("arguments[0].scrollIntoView();", test2)
    time.sleep(10)
    test2.click()
    time.sleep(15)
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys()
    driver.find_element(By.XPATH, '//button[text() = "Send"]').send_keys(Keys.ENTER)
    time.sleep(3)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()

def test_reserv_func5(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    time.sleep(3)
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[1]').click()
    time.sleep(5)
    assert driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').is_displayed()

def test_reserv_eh1(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    time.sleep(8)
    driver.find_element(By.XPATH, "//*[contains(text(),'Kids')]").click()
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys(0)
    order = driver.find_element(By.XPATH, '//button[text() = "Send"]')
    time.sleep(5)
    order.send_keys(Keys.ENTER)
    time.sleep(3)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()

def test_reserv_eh2(setup):
    driver = setup
    a = ActionChains(driver)
    driver.find_element(By.XPATH, '//a[@href="#/SignIn"]').click()
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your email"]').send_keys("ofir123@walla.com")
    driver.find_element(By.XPATH, '//input[@placeholder="Enter your password"]').send_keys("ofir1234")
    driver.find_element(By.XPATH, '//button[text() = "Sign in"]').click()
    time.sleep(1)
    test = driver.find_element(By.XPATH , '//h2[text()="SVBURGER WEATHER"]')
    time.sleep(5)
    driver.find_element(By.XPATH, "//*[contains(text(),'Combo')]").click()
    time.sleep(8)
    driver.find_element(By.XPATH, "//*[contains(text(),'Kids')]").click()
    time.sleep(8)
    test2 = driver.find_element(By.XPATH,'//*[contains(text(),"Vegan")]')
    time.sleep(5)
    fries = driver.find_element(By.XPATH, "//h5[contains(text(), 'Sides')]")
    time.sleep(8)
    driver.execute_script("arguments[0].scrollIntoView();", test2)
    time.sleep(10)
    test2.click()
    time.sleep(8)
    fries.click()
    time.sleep(8)
    driver.execute_script("arguments[0].scrollIntoView();", test)
    time.sleep(3)
    driver.find_element(By.XPATH,'//button[2]').click()
    time.sleep(3)
    driver.find_element(By.XPATH, '//input[@index="0"]').send_keys()
    driver.find_element(By.XPATH, '//button[text() = "Send"]').send_keys(Keys.ENTER)
    time.sleep(3)
    assert driver.find_element(By.XPATH, '//h4[text() = "Your order will arrive to your table in ..."]').is_displayed()



