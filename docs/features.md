# Key Features

Pydoll brings groundbreaking capabilities to browser automation, making it significantly more powerful than traditional tools while being easier to use.

## Core Capabilities

### Zero WebDrivers

Unlike traditional browser automation tools like Selenium, Pydoll eliminates the need for WebDrivers entirely. By connecting directly to browsers through the Chrome DevTools Protocol, Pydoll:

- Eliminates version compatibility issues between browser and driver
- Reduces setup complexity and maintenance overhead
- Provides more reliable connections without driver-related issues
- Allows for automation of all Chromium-based browsers with a unified API

No more "chromedriver version doesn't match Chrome version" errors or mysterious webdriver crashes.

### Async-First Architecture

Built from the ground up with Python's asyncio, Pydoll provides:

- **True Concurrency**: Run multiple operations in parallel without blocking
- **Efficient Resource Usage**: Manage many browser instances with minimal overhead
- **Modern Python Patterns**: Context managers, async iterators, and other asyncio-friendly interfaces
- **Performance Optimizations**: Reduced latency and increased throughput for automation tasks

### Human-Like Interactions

Avoid detection by mimicking real user behavior:

- **Natural Typing**: Type text with randomized timing between keystrokes
- **Realistic clicking**: Click with realistic timing and movement, including offset

### Event-Driven Capabilities

Respond to browser events in real-time:

- **Network Monitoring**: Track requests, responses, and failed loads
- **DOM Observation**: React to changes in the page structure
- **Page Lifecycle Events**: Capture navigation, loading, and rendering events
- **Custom Event Handlers**: Register callbacks for specific events of interest

### Multi-Browser Support

Pydoll works seamlessly with:

- **Google Chrome**: Primary support with all features available
- **Microsoft Edge**: Full support for Edge-specific features
- **Chromium**: Support for other Chromium-based browsers

### Screenshot and PDF Export

Capture visual content from web pages:

- **Full Page Screenshots**: Capture entire page content, even beyond the viewport
- **Element Screenshots**: Target specific elements for capture
- **High-Quality PDF Export**: Generate PDF documents from web pages
- **Custom Formatting**: Coming soon!

## Intuitive Element Finding

Pydoll v2.0+ introduces a revolutionary approach to finding elements that's both more intuitive and more powerful than traditional selector-based methods.

### Modern find() Method

The new `find()` method allows you to search for elements using natural attributes:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def element_finding_examples():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com')
        
        # Find by tag name and class
        submit_button = await tab.find(tag_name='button', class_name='btn-primary')
        
        # Find by ID (most common)
        username_field = await tab.find(id='username')
        
        # Find by text content
        login_link = await tab.find(tag_name='a', text='Login')
        
        # Find by multiple attributes
        search_input = await tab.find(
            tag_name='input',
            type='text',
            placeholder='Search...'
        )
        
        # Find with custom data attributes
        custom_element = await tab.find(
            data_testid='submit-button',
            aria_label='Submit form'
        )
        
        # Find multiple elements
        all_links = await tab.find(tag_name='a', find_all=True)
        
        # With timeout and error handling
        delayed_element = await tab.find(
            class_name='dynamic-content',
            timeout=10,
            raise_exc=False  # Returns None if not found
        )

asyncio.run(element_finding_examples())
```

### CSS Selectors and XPath with query()

For developers who prefer traditional selectors, the `query()` method provides direct CSS selector and XPath support:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def query_examples():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com')
        
        # CSS selectors
        nav_menu = await tab.query('nav.main-menu')
        first_article = await tab.query('article:first-child')
        submit_button = await tab.query('button[type="submit"]')
        
        # XPath expressions
        specific_item = await tab.query('//div[@data-testid="item-123"]')
        text_content = await tab.query('//span[contains(text(), "Welcome")]')
        
        # Complex selectors
        nested_element = await tab.query('div.container > .content .item:nth-child(2)')

asyncio.run(query_examples())
```

## Native Cloudflare Captcha Bypass

!!! warning "Important Information About Captcha Bypass"
    The effectiveness of Cloudflare Turnstile bypass depends on several factors:
    
    - **IP Reputation**: Cloudflare assigns a "trust score" to each IP address. Clean residential IPs typically receive higher scores.
    - **Previous History**: IPs with a history of suspicious activity may be permanently flagged.
    
    Pydoll can achieve scores comparable to a regular browser session, but cannot overcome IP-based blocks or extremely restrictive configurations. For best results, use residential IPs with good reputation.
    
    Remember that captcha bypass techniques operate in a gray area and should be used responsibly.

One of Pydoll's most powerful features is its ability to automatically bypass Cloudflare Turnstile captchas that block most automation tools:

### Context Manager Approach (Synchronous)

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def bypass_cloudflare_example():
    async with Chrome() as browser:
        tab = await browser.start()
    
    # The context manager will wait for the captcha to be processed
    # before continuing execution
        async with tab.expect_and_bypass_cloudflare_captcha():
            await tab.go_to('https://site-with-cloudflare.com')
        print("Waiting for captcha to be handled...")
    
    # This code runs only after the captcha is successfully bypassed
    print("Captcha bypassed! Continuing with automation...")
        protected_content = await tab.find(id='protected-content')
        content_text = await protected_content.text
        print(f"Protected content: {content_text}")

asyncio.run(bypass_cloudflare_example())
```

### Background Processing Approach

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def background_bypass_example():
    async with Chrome() as browser:
        tab = await browser.start()
    
    # Enable automatic captcha solving before navigating
        await tab.enable_auto_solve_cloudflare_captcha()
    
    # Navigate to the protected site - captcha handled automatically in background
        await tab.go_to('https://site-with-cloudflare.com')
    print("Page loaded, captcha will be handled in the background...")
    
    # Add a small delay to allow captcha solving to complete
    await asyncio.sleep(3)
    
    # Continue with automation
        protected_content = await tab.find(id='protected-content')
        content_text = await protected_content.text
        print(f"Protected content: {content_text}")
    
    # Disable auto-solving when no longer needed
        await tab.disable_auto_solve_cloudflare_captcha()

asyncio.run(background_bypass_example())
```

Access websites that actively block automation tools without using third-party captcha solving services. This native captcha handling makes Pydoll suitable for automating previously inaccessible websites.

## Multi-Tab Management

Pydoll provides sophisticated tab management capabilities with a singleton pattern that ensures efficient resource usage and prevents duplicate Tab instances for the same browser tab.

### Tab Singleton Pattern

Pydoll implements a singleton pattern for Tab instances based on the browser's target ID. This means:

- **One Tab instance per browser tab**: Multiple references to the same browser tab return the same Tab object
- **Automatic resource management**: No duplicate connections or handlers for the same tab
- **Consistent state**: All references to a tab share the same state and event handlers

```python
import asyncio
from pydoll.browser.chromium import Chrome
from pydoll.browser.tab import Tab

async def singleton_demonstration():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Get the same tab through different methods - they're identical objects
        same_tab = Tab(browser, browser._connection_port, tab._target_id)
        opened_tabs = await browser.get_opened_tabs()
        
        # All references point to the same singleton instance
        print(f"Same object? {tab is same_tab}")  # May be True if same target_id
        print(f"Tab instances are managed as singletons")

asyncio.run(singleton_demonstration())
```

### Creating New Tabs Programmatically

Use `new_tab()` to create tabs programmatically with full control:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def programmatic_tab_creation():
    async with Chrome() as browser:
        # Start with the initial tab
        main_tab = await browser.start()
        
        # Create additional tabs with specific URLs
        search_tab = await browser.new_tab('https://google.com')
        news_tab = await browser.new_tab('https://news.ycombinator.com')
        docs_tab = await browser.new_tab('https://docs.python.org')
        
        # Work with multiple tabs simultaneously
        await search_tab.find(name='q').type_text('Python automation')
        await news_tab.find(class_name='storylink', find_all=True)
        await docs_tab.find(id='search-field').type_text('asyncio')
        
        # Get all opened tabs
        all_tabs = await browser.get_opened_tabs()
        print(f"Total tabs open: {len(all_tabs)}")
        
        # Close specific tabs when done
        await search_tab.close()
        await news_tab.close()

asyncio.run(programmatic_tab_creation())
```

### Handling User-Opened Tabs

When users click links that open new tabs (target="_blank"), use `get_opened_tabs()` to detect and manage them:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def handle_user_opened_tabs():
    async with Chrome() as browser:
        main_tab = await browser.start()
        await main_tab.go_to('https://example.com')
        
        # Get initial tab count
        initial_tabs = await browser.get_opened_tabs()
        initial_count = len(initial_tabs)
        print(f"Initial tabs: {initial_count}")
        
        # Click a link that opens a new tab (target="_blank")
        external_link = await main_tab.find(text='Open in New Tab')
        await external_link.click()
        
        # Wait for new tab to open
        await asyncio.sleep(2)
        
        # Detect new tabs
        current_tabs = await browser.get_opened_tabs()
        new_tab_count = len(current_tabs)
        
        if new_tab_count > initial_count:
            print(f"New tab detected! Total tabs: {new_tab_count}")
            
            # Get the newly opened tab (last in the list)
            new_tab = current_tabs[-1]
            
            # Work with the new tab
            await new_tab.go_to('https://different-site.com')
            title = await new_tab.execute_script('return document.title')
            print(f"New tab title: {title}")
            
            # Close the new tab when done
            await new_tab.close()

asyncio.run(handle_user_opened_tabs())
```

### Key Benefits of Pydoll's Tab Management

1. **Singleton Pattern**: Prevents resource duplication and ensures consistent state
2. **Automatic Detection**: `get_opened_tabs()` finds all tabs, including user-opened ones
3. **Concurrent Processing**: Handle multiple tabs simultaneously with asyncio
4. **Resource Management**: Proper cleanup prevents memory leaks
5. **Event Isolation**: Each tab maintains its own event handlers and state

This sophisticated tab management makes Pydoll ideal for:
- **Multi-page workflows** that require coordination between tabs
- **Parallel data extraction** from multiple sources
- **Testing applications** that use popup windows or new tabs
- **Monitoring user behavior** across multiple browser tabs


## Concurrent Scraping

Pydoll's async architecture allows you to scrape multiple pages or websites simultaneously for maximum efficiency:

```python
import asyncio
from functools import partial
from pydoll.browser.chromium import Chrome

async def scrape_page(browser, url):
    """Process a single page and extract data using a shared browser"""
    # Create a new tab for this URL
    tab = await browser.new_tab()
    
    try:
        await tab.go_to(url)
        
        # Extract data
        title = await tab.execute_script('return document.title')
        
        # Find elements and extract content
        elements = await tab.find(class_name='article-content', find_all=True)
        content = []
        for element in elements:
            text = await element.text
            content.append(text)
            
        return {
            "url": url,
            "title": title,
            "content": content
        }
    finally:
        # Close the tab when done to free resources
        await tab.close()

async def main():
    # List of URLs to scrape in parallel
    urls = [
        'https://example.com/page1',
        'https://example.com/page2',
        'https://example.com/page3',
        'https://example.com/page4',
        'https://example.com/page5',
    ]
    
    async with Chrome() as browser:
        # Start the browser once
        await browser.start()
        
        # Create partial function with browser parameter
        scrape_with_browser = partial(scrape_page, browser)
        
        # Process all URLs concurrently using the same browser
        results = await asyncio.gather(*(scrape_with_browser(url) for url in urls))
    
    # Print results
    for result in results:
        print(f"Scraped {result['url']}: {result['title']}")
        print(f"Found {len(result['content'])} content blocks")
    
    return results

# Run the concurrent scraping
all_data = asyncio.run(main())
```

This approach provides dramatic performance improvements over sequential scraping, especially for I/O-bound tasks like web scraping. Instead of waiting for each page to load one after another, Pydoll processes them all simultaneously, reducing total execution time significantly. For example, scraping 10 pages that each take 2 seconds to load would take just over 2 seconds total instead of 20+ seconds with sequential processing.

## Advanced Keyboard Control

Pydoll provides human-like keyboard interaction with precise control over typing behavior:

```python
import asyncio
from pydoll.browser.chromium import Chrome
from pydoll.common.keys import Keys

async def realistic_typing_example():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/login')
        
        # Find login form elements
        username = await tab.find(id='username')
        password = await tab.find(id='password')
        
        # Type with realistic timing (interval between keystrokes)
        await username.type_text("user@example.com", interval=0.15)
        
        # Use special key combinations
        await password.click()
        await password.key_down(Keys.SHIFT)
        await password.type_text("PASSWORD")
        await password.key_up(Keys.SHIFT)
        
        # Press Enter to submit
        await password.press_keyboard_key(Keys.ENTER)
        
        # Wait for navigation
        await asyncio.sleep(2)
        print("Logged in successfully!")

asyncio.run(realistic_typing_example())
```

This realistic typing helps avoid detection by websites that look for automation patterns. The natural timing and ability to use special key combinations makes Pydoll's interactions virtually indistinguishable from human users.

## Powerful Event System

Pydoll's event system allows you to react to browser events in real-time:

```python
import asyncio
from pydoll.browser.chromium import Chrome
from pydoll.protocol.page.events import PageEvent

async def event_monitoring_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Monitor page load events
        async def on_page_loaded(event):
            print(f"🌐 Page loaded: {event['params'].get('url')}")
            
        await tab.enable_page_events()
        await tab.on(PageEvent.LOAD_EVENT_FIRED, on_page_loaded)
        
        # Monitor network requests
        async def on_request(event):
            url = event['params']['request']['url']
            print(f"🔄 Request to: {url}")
            
        await tab.enable_network_events()
        await tab.on('Network.requestWillBeSent', on_request)
        
        # Navigate and see events in action
        await tab.go_to('https://example.com')
        await asyncio.sleep(5)  # Allow time to see events
        
asyncio.run(event_monitoring_example())
```

The event system makes Pydoll uniquely powerful for monitoring API requests and responses, creating reactive automations, debugging complex web applications, and building comprehensive web monitoring tools.

## Network Analysis and Response Extraction

Pydoll provides powerful methods for analyzing network traffic and extracting response data from web applications. These capabilities are essential for API monitoring, data extraction, and debugging network-related issues.

### Network Logs Analysis

The `get_network_logs()` method allows you to retrieve and analyze all network requests made by a page:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def network_analysis_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Enable network monitoring
        await tab.enable_network_events()
        
        # Navigate to a page with API calls
        await tab.go_to('https://example.com/dashboard')
        
        # Wait for page to load and make requests
        await asyncio.sleep(3)
        
        # Get all network logs
        all_logs = await tab.get_network_logs()
        print(f"Total network requests: {len(all_logs)}")
        
        # Filter logs for API requests only
        api_logs = await tab.get_network_logs(filter='api')
        print(f"API requests: {len(api_logs)}")
        
        # Filter logs for specific domain
        domain_logs = await tab.get_network_logs(filter='example.com')
        print(f"Requests to example.com: {len(domain_logs)}")
        
        # Analyze request patterns
        for log in api_logs:
            request = log['params'].get('request', {})
            url = request.get('url', 'Unknown')
            method = request.get('method', 'Unknown')
            print(f"📡 {method} {url}")

asyncio.run(network_analysis_example())
```

### Response Body Extraction

The `get_network_response_body()` method enables you to extract the actual response content from network requests:

```python
import asyncio
import json
from functools import partial
from pydoll.browser.chromium import Chrome
from pydoll.protocol.network.events import NetworkEvent

async def response_extraction_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Storage for API responses
        api_responses = {}
        
        async def capture_api_responses(tab, event):
            """Capture API response bodies"""
            request_id = event['params']['requestId']
            response = event['params']['response']
            url = response['url']
            
            # Only capture successful API responses
            if '/api/' in url and response['status'] == 200:
                try:
                    # Extract the response body
                    body = await tab.get_network_response_body(request_id)
                    
                    # Try to parse as JSON
                    try:
                        data = json.loads(body)
                        api_responses[url] = data
                        print(f"✅ Captured API response from: {url}")
                        print(f"Data keys: {list(data.keys()) if isinstance(data, dict) else 'Non-dict response'}")
                    except json.JSONDecodeError:
                        # Handle non-JSON responses
                        api_responses[url] = body
                        print(f"📄 Captured text response from: {url} ({len(body)} chars)")
                        
                except Exception as e:
                    print(f"❌ Failed to get response body for {url}: {e}")
        
        # Enable network monitoring and register callback
        await tab.enable_network_events()
        await tab.on(NetworkEvent.RESPONSE_RECEIVED, partial(capture_api_responses, tab))
        
        # Navigate to a page with API calls
        await tab.go_to('https://jsonplaceholder.typicode.com')
        
        # Trigger some API calls by interacting with the page
        await asyncio.sleep(5)
        
        # Display captured responses
        print(f"\n📊 Analysis Results:")
        print(f"Captured {len(api_responses)} API responses")
        
        for url, data in api_responses.items():
            if isinstance(data, dict):
                print(f"🔗 {url}: {len(data)} fields")
            else:
                print(f"🔗 {url}: {len(str(data))} characters")
        
        return api_responses

asyncio.run(response_extraction_example())
```

### Advanced Network Monitoring

Combine both methods for comprehensive network analysis:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def comprehensive_network_monitoring():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Enable network monitoring
        await tab.enable_network_events()
        
        # Navigate to a complex web application
        await tab.go_to('https://example.com/app')
        
        # Wait for initial page load and API calls
        await asyncio.sleep(5)
        
        # Get comprehensive network analysis
        all_logs = await tab.get_network_logs()
        api_logs = await tab.get_network_logs(filter='api')
        static_logs = await tab.get_network_logs(filter='.js')
        
        print(f"📈 Network Traffic Summary:")
        print(f"   Total requests: {len(all_logs)}")
        print(f"   API calls: {len(api_logs)}")
        print(f"   JavaScript files: {len(static_logs)}")
        
        # Analyze request types
        request_types = {}
        for log in all_logs:
            request = log['params'].get('request', {})
            url = request.get('url', '')
            
            if '/api/' in url:
                request_types['API'] = request_types.get('API', 0) + 1
            elif any(ext in url for ext in ['.js', '.css', '.png', '.jpg']):
                request_types['Static'] = request_types.get('Static', 0) + 1
            else:
                request_types['Other'] = request_types.get('Other', 0) + 1
        
        print(f"📊 Request breakdown: {request_types}")
        
        # Show API endpoints
        print(f"\n🔗 API Endpoints Called:")
        for log in api_logs[:10]:  # Show first 10
            request = log['params'].get('request', {})
            method = request.get('method', 'GET')
            url = request.get('url', 'Unknown')
            print(f"   {method} {url}")

asyncio.run(comprehensive_network_monitoring())
```

These network analysis capabilities make Pydoll ideal for:

- **API Testing**: Monitor and validate API responses
- **Performance Analysis**: Track request timing and sizes
- **Data Extraction**: Extract dynamic content loaded via AJAX
- **Debugging**: Identify failed requests and network issues
- **Security Testing**: Analyze request/response patterns

## Custom Browser Preferences

Pydoll now supports advanced browser customization through the `ChromiumOptions.browser_preferences` property. This allows you to set any Chromium browser preference for your automation session.

### What You Can Customize
- Download directory and prompt behavior
- Accepted languages
- Notification blocking
- PDF handling
- Any other Chromium-supported preference

### Example: Setting Preferences
```python
from pydoll.browser.chromium import Chrome
from pydoll.browser.options import ChromiumOptions

options = ChromiumOptions()
options.browser_preferences = {
    'download': {
        'default_directory': '/tmp/downloads',
        'prompt_for_download': False
    },
    'intl': {
        'accept_languages': 'en-US,en,pt-BR'
    },
    'profile': {
        'default_content_setting_values': {
            'notifications': 2  # Block notifications
        }
    }
}

# Helper methods for common preferences
options.set_default_download_directory('/tmp/downloads')
options.set_accept_languages('en-US,en,pt-BR')
options.prompt_for_download = False

browser = Chrome(options=options)
```

You can call `browser_preferences` multiple times—new keys will be merged, not replaced.

### Why is this useful?
- Fine-grained control for scraping, testing, or automation
- Avoid popups or unwanted prompts
- Match user locale or automate downloads

---

## File Upload Support

Seamlessly handle file uploads in your automation:

```python
import asyncio
import os
from pydoll.browser.chromium import Chrome

async def file_upload_example():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/upload')
        
        # Method 1: Direct file input
        file_input = await tab.find(tag_name='input', type='file')
        await file_input.set_input_files('path/to/document.pdf')
        
        # Method 2: Using file chooser with an upload button
        sample_file = os.path.join(os.getcwd(), 'sample.jpg')
        async with tab.expect_file_chooser(files=sample_file):
            upload_button = await tab.find(id='upload-button')
            await upload_button.click()
            
        # Submit the form
        submit = await tab.find(id='submit-button')
        await submit.click()
        
        print("Files uploaded successfully!")

asyncio.run(file_upload_example())
```

File uploads are notoriously difficult to automate in other frameworks, often requiring workarounds. Pydoll makes it straightforward with both direct file input and file chooser dialog support.

## Multi-Browser Example

Pydoll works with different browsers through a consistent API:

```python
import asyncio
from pydoll.browser.chromium import Chrome, Edge

async def multi_browser_example():
    # Run the same automation in Chrome
    async with Chrome() as chrome:
        chrome_tab = await chrome.start()
        await chrome_tab.go_to('https://example.com')
        chrome_title = await chrome_tab.execute_script('return document.title')
        print(f"Chrome title: {chrome_title}")
    
    # Run the same automation in Edge
    async with Edge() as edge:
        edge_tab = await edge.start()
        await edge_tab.go_to('https://example.com')
        edge_title = await edge_tab.execute_script('return document.title')
        print(f"Edge title: {edge_title}")

asyncio.run(multi_browser_example())
```

Cross-browser compatibility without changing your code. Test your automations across different browsers to ensure they work everywhere.

## Proxy Integration

Unlike many automation tools that struggle with proxy implementation, Pydoll offers native proxy support with full authentication capabilities. This makes it ideal for:

- **Web scraping** projects that need to rotate IPs
- **Geo-targeted testing** of applications across different regions
- **Privacy-focused automation** that requires anonymizing traffic
- **Testing web applications** through corporate proxies

Configuring proxies in Pydoll is straightforward:

```python
import asyncio
from pydoll.browser.chromium import Chrome
from pydoll.browser.options import ChromiumOptions

async def proxy_example():
    # Create browser options
    options = ChromiumOptions()
    
    # Simple proxy without authentication
    options.add_argument('--proxy-server=192.168.1.100:8080')
    # Or proxy with authentication
    # options.add_argument('--proxy-server=username:password@192.168.1.100:8080')
    
    # Bypass proxy for specific domains
    options.add_argument('--proxy-bypass-list=*.internal.company.com,localhost')

    # Start browser with proxy configuration
    async with Chrome(options=options) as browser:
        tab = await browser.start()
        
        # Test the proxy by visiting an IP echo service
        await tab.go_to('https://api.ipify.org')
        ip_address = await tab.execute_script('return document.body.textContent')
        print(f"Current IP address: {ip_address}")
        
        # Continue with your automation
        await tab.go_to('https://example.com')
        title = await tab.execute_script('return document.title')
        print(f"Page title: {title}")

asyncio.run(proxy_example())
```

## Working with iFrames

Pydoll provides seamless iframe interaction through the `get_frame()` method:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def iframe_interaction():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/page-with-iframe')
        
        # Find the iframe element
        iframe_element = await tab.query('.hcaptcha-iframe', timeout=10)
        
        # Get a Tab instance for the iframe content
        frame = await tab.get_frame(iframe_element)
        
        # Now interact with elements inside the iframe
        submit_button = await frame.find(tag_name='button', class_name='submit')
        await submit_button.click()
        
        # You can use all Tab methods on the frame
        form_input = await frame.find(id='captcha-input')
        await form_input.type_text('verification-code')
        
        # Find elements by various methods
        links = await frame.find(tag_name='a', find_all=True)
        specific_element = await frame.query('#specific-id')

asyncio.run(iframe_interaction())
```

## Request Interception

Intercept and modify network requests before they're sent:

### Basic Request Modification

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def request_interception_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Define the request interceptor
        async def intercept_request(event):
            request_id = event['params']['requestId']
            url = event['params']['request']['url']
            
            if '/api/' in url:
                # Get original headers
                original_headers = event['params']['request'].get('headers', {})
                
                # Add custom headers
                custom_headers = {
                    **original_headers,
                    'Authorization': 'Bearer my-token-123',
                    'X-Custom-Header': 'CustomValue'
                }
                
                print(f"🔄 Modifying request to: {url}")
                await tab.continue_request(
                        request_id=request_id,
                        headers=custom_headers
                )
            else:
                # Continue normally for non-API requests
                await tab.continue_request(request_id=request_id)
        
        # Enable interception and register handler
        await tab.enable_request_interception()
        await tab.on('Fetch.requestPaused', intercept_request)
        
        # Navigate to trigger requests
        await tab.go_to('https://example.com')
        await asyncio.sleep(5)  # Allow time for requests to process

asyncio.run(request_interception_example())
```

### Blocking Unwanted Requests

Use `fail_request` to block specific requests like ads, trackers, or unwanted resources:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def block_requests_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        # Define blocked domains and resource types
        blocked_domains = ['doubleclick.net', 'googletagmanager.com', 'facebook.com']
        blocked_resources = ['image', 'stylesheet', 'font']
        
        async def block_unwanted_requests(event):
            request_id = event['params']['requestId']
            url = event['params']['request']['url']
            resource_type = event['params'].get('resourceType', '').lower()
            
            # Block requests from specific domains
            if any(domain in url for domain in blocked_domains):
                print(f"🚫 Blocking request to: {url}")
                await tab.fail_request(
                    request_id=request_id,
                    error_reason='BlockedByClient'
                )
                return
            
            # Block specific resource types (images, CSS, fonts)
            if resource_type in blocked_resources:
                print(f"🚫 Blocking {resource_type}: {url}")
                await tab.fail_request(
                    request_id=request_id,
                    error_reason='BlockedByClient'
                )
                return
            
            # Continue with allowed requests
            await tab.continue_request(request_id=request_id)
        
        # Enable interception and register handler
        await tab.enable_request_interception()
        await tab.on('Fetch.requestPaused', block_unwanted_requests)
        
        # Navigate to a page with many external resources
        await tab.go_to('https://example.com')
        await asyncio.sleep(10)  # Allow time to see blocked requests

asyncio.run(block_requests_example())
```

### Mocking API Responses

Use `fulfill_request` to return custom responses without making actual network requests:

```python
import asyncio
import json
from pydoll.browser.chromium import Chrome

async def mock_api_responses_example():
    async with Chrome() as browser:
        tab = await browser.start()
        
        async def mock_api_requests(event):
            request_id = event['params']['requestId']
            url = event['params']['request']['url']
            
            # Mock user API endpoint
            if '/api/user' in url:
                mock_user_data = {
                    "id": 123,
                    "name": "John Doe",
                    "email": "john@example.com",
                    "role": "admin"
                }
                
                print(f"🎭 Mocking API response for: {url}")
                await tab.fulfill_request(
                    request_id=request_id,
                    response_code=200,
                    response_headers={
                        'Content-Type': 'application/json',
                        'Access-Control-Allow-Origin': '*'
                    },
                    body=json.dumps(mock_user_data)
                )
                return
            
            # Mock products API endpoint
            elif '/api/products' in url:
                mock_products = [
                    {"id": 1, "name": "Product A", "price": 29.99},
                    {"id": 2, "name": "Product B", "price": 39.99},
                    {"id": 3, "name": "Product C", "price": 19.99}
                ]
                
                print(f"🎭 Mocking products API response for: {url}")
                await tab.fulfill_request(
                    request_id=request_id,
                    response_code=200,
                    response_headers={'Content-Type': 'application/json'},
                    body=json.dumps(mock_products)
                )
                return
            
            # Simulate API error for specific endpoints
            elif '/api/error' in url:
                error_response = {"error": "Internal Server Error", "code": 500}
                
                print(f"🎭 Mocking error response for: {url}")
                await tab.fulfill_request(
                    request_id=request_id,
                    response_code=500,
                    response_headers={'Content-Type': 'application/json'},
                    body=json.dumps(error_response)
                )
                return
            
            # Continue with real requests for everything else
            await tab.continue_request(request_id=request_id)
        
        # Enable interception and register handler
        await tab.enable_request_interception()
        await tab.on('Fetch.requestPaused', mock_api_requests)
        
        # Navigate to a page that makes API calls
        await tab.go_to('https://example.com/dashboard')
        await asyncio.sleep(5)  # Allow time for API calls

asyncio.run(mock_api_responses_example())
```

### Advanced Request Manipulation

Combine all interception methods for comprehensive request control:

```python
import asyncio
import json
from pydoll.browser.chromium import Chrome

async def advanced_request_control():
    async with Chrome() as browser:
        tab = await browser.start()
        
        async def advanced_interceptor(event):
            request_id = event['params']['requestId']
            url = event['params']['request']['url']
            method = event['params']['request']['method']
            headers = event['params']['request'].get('headers', {})
            
            print(f"📡 Intercepted {method} request to: {url}")
            
            # Block analytics and tracking
            if any(tracker in url for tracker in ['analytics', 'tracking', 'ads']):
                print(f"🚫 Blocked tracking request: {url}")
                await tab.fail_request(request_id=request_id, error_reason='BlockedByClient')
                return
            
            # Mock authentication endpoint
            if '/auth/login' in url and method == 'POST':
                mock_auth_response = {
                    "success": True,
                    "token": "mock-jwt-token-12345",
                    "user": {"id": 1, "username": "testuser"}
                }
                print(f"🎭 Mocking login response")
                await tab.fulfill_request(
                    request_id=request_id,
                    response_code=200,
                    response_headers={'Content-Type': 'application/json'},
                    body=json.dumps(mock_auth_response)
                )
                return
            
            # Add authentication to API requests
            if '/api/' in url and 'Authorization' not in headers:
                modified_headers = {
                    **headers,
                    'Authorization': 'Bearer mock-token-12345',
                    'X-Test-Mode': 'true'
                }
                print(f"🔧 Adding auth headers to: {url}")
                await tab.continue_request(
                    request_id=request_id,
                    headers=modified_headers
                )
                return
            
            # Continue with unmodified request
            await tab.continue_request(request_id=request_id)
        
        # Enable interception
        await tab.enable_request_interception()
        await tab.on('Fetch.requestPaused', advanced_interceptor)
        
        # Test the interception
        await tab.go_to('https://example.com/app')
        await asyncio.sleep(10)

asyncio.run(advanced_request_control())
```

This powerful capability allows you to:

- **Add authentication headers dynamically** for API requests
- **Block unwanted resources** like ads, trackers, and heavy images for faster loading
- **Mock API responses** for testing without backend dependencies
- **Simulate network errors** to test error handling
- **Modify request payloads** before they're sent
- **Analyze and debug network traffic** in real-time

Each of these features showcases what makes Pydoll a next-generation browser automation tool, combining the power of direct browser control with an intuitive, async-native API. 