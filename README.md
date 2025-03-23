# WhatsAppWebScrape
This project automates the extraction of contact information (phone numbers and saved names) from WhatsApp Web using Python, Selenium, and BeautifulSoup. It is designed to tackle the challenge of collecting contact data from a large number of chats (up to 5,000), which would be extremely time-consuming and error-prone if done manually.

## Project Description

This project automates the extraction of contact information (phone numbers and saved names) from WhatsApp Web using Python, Selenium, and BeautifulSoup. It is designed to tackle the challenge of collecting contact data from a large number of chats (up to 5,000), which would be extremely time-consuming and error-prone if done manually.

## Problem Statement

WhatsApp Web only loads a limited number of chats (around 500 at a time) due to its virtualization technique, where only a fixed number of chat items (about 13) are present in the DOM at once. As the user scrolls, these DOM elements are recycled and populated with new data, making it difficult to capture all contacts in one go.

## Solution Approach

The project uses Selenium to:

1. Automate Scrolling: Gradually scrolls through the chat list in small increments to ensure that the DOM elements update with new data.

2. Capture Contact Information: Uses BeautifulSoup to extract phone numbers and saved names from the loaded chat items.

3. Deduplicate Data: Utilizes Python sets to automatically filter out duplicates.

4. Export to CSV: Saves the final list of unique contacts in a CSV file.


## Technical Challenges and Solutions

Dynamic DOM Elements: The project handles virtualized DOM by scrolling in small steps (20 pixels at a time) with pauses to let new content load.

Duplicate Entries: By collecting data into a set, the project ensures that only unique contacts are stored.

Performance Optimization: The script balances between speed and data accuracy by using small scroll increments and moderate pause times.


## Technologies Used

Python: Main programming language.

Selenium: Automates browser interactions.

BeautifulSoup: Extracts content from the HTML page.

CSV: Stores the extracted contacts.

ChromeDriver: Controls the Chrome browser.

## CODE:
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.common.keys import Keys
    import time
    import csv
    import os
    
    # Setup Selenium WebDriver with Chrome and the saved WhatsApp profile
    options = webdriver.ChromeOptions()
    options.add_argument("user-data-dir=/path/to/your/chrome/profile")  # Update this path to your Chrome profile directory
    driver = webdriver.Chrome(options=options)
    
    # Open WhatsApp Web
    driver.get("https://web.whatsapp.com")
    time.sleep(15)  # Wait for manual login if needed

    # Prepare to store the scraped data
    contacts = set()
    output_file = "/Users/abdullrahman/Desktop/whatsapp_contacts.csv"

    # Scroll settings
    scroll_pause_time = 2  # Pause between scrolls
    scroll_increment = 20  # Pixels to scroll each time
    total_iterations = 273  # Approximate number of iterations to cover 5000 chats

    # Open CSV file for writing
    with open(output_file, "w", newline="", encoding="utf-8") as file:
    writer = csv.writer(file)
    writer.writerow(["Contact Name or Number"])

    # Start the scrolling and extraction process
    for iteration in range(total_iterations):
        print(f"Iteration {iteration + 1}/{total_iterations}")

        # Locate chat elements on the screen
        chat_elements = driver.find_elements(By.XPATH, "//div[@class='x1n2onr6']//span[@class='x1iyjqo2 x6ikm8r x10wlt62 x1n2onr6 xlyipyv xuxw1ft x1rg5ohu _ao3e']")
        
        # Extract text from chat elements
        for chat in chat_elements:
            contact_info = chat.text
            if contact_info not in contacts and contact_info:
                print(contact_info)
                contacts.add(contact_info)
                writer.writerow([contact_info])

        # Scroll down by a small increment
        chat_list = driver.find_element(By.XPATH, "//div[@class='x1n2onr6']")
        driver.execute_script(f"arguments[0].scrollBy(0, {scroll_increment});", chat_list)
        time.sleep(scroll_pause_time)

    print("Scraping completed.")
    driver.quit()

## Disclaimer

This project is for educational and personal use only. Make sure to follow applicable legal and ethical guidelines before scraping data from WhatsApp Web.

