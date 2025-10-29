# File Operations

File uploads are one of the most challenging aspects of browser automation. Traditional tools often struggle with OS-level file dialogs, requiring complex workarounds or external libraries. Pydoll provides two straightforward approaches for handling file uploads, each suited for different scenarios.

## Upload Methods

Pydoll supports two primary methods for file uploads:

1. **Direct file input** (`set_input_files()`): Fast and direct, works with `<input type="file">` elements
2. **File chooser context manager** (`expect_file_chooser()`): Intercepts the file dialog, works with any upload trigger

## Direct File Input

The simplest approach is using `set_input_files()` directly on file input elements. This method is fast, reliable, and bypasses the OS file dialog entirely.

### Basic Usage

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def direct_file_upload():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/upload')
        
        # Find the file input element
        file_input = await tab.find(tag_name='input', type='file')
        
        # Set the file directly
        await file_input.set_input_files(['path/to/document.pdf'])
        
        # Submit the form
        submit_button = await tab.find(id='submit-button')
        await submit_button.click()
        
        print("File uploaded successfully!")

asyncio.run(direct_file_upload())
```

### Multiple Files

For inputs that accept multiple files (`<input type="file" multiple>`), pass a list of file paths:

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def upload_multiple_files():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/multi-upload')
        
        file_input = await tab.find(tag_name='input', type='file')
        
        # Upload multiple files at once
        files = [
            'documents/report.pdf',
            'images/screenshot.png',
            'data/results.csv'
        ]
        await file_input.set_input_files(files)
        
        # Process as normal
        upload_btn = await tab.find(id='upload-btn')
        await upload_btn.click()

asyncio.run(upload_multiple_files())
```

### Using Path Objects

Both string paths and `Path` objects from `pathlib` are supported:

```python
import asyncio
from pathlib import Path
from pydoll.browser.chromium import Chrome

async def upload_with_path_objects():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/upload')
        
        file_input = await tab.find(tag_name='input', type='file')
        
        # Using Path objects
        project_dir = Path(__file__).parent
        file_path = project_dir / 'uploads' / 'data.json'
        
        await file_input.set_input_files([str(file_path)])

asyncio.run(upload_with_path_objects())
```

!!! tip "When to Use Direct File Input"
    Use `set_input_files()` when:
    
    - The file input is directly accessible in the DOM
    - You want maximum speed and simplicity
    - The upload doesn't trigger a file chooser dialog
    - You're working with standard `<input type="file">` elements

## File Chooser Context Manager

Some websites hide the file input and use custom buttons or drag-and-drop areas that trigger the OS file chooser dialog. For these cases, use the `expect_file_chooser()` context manager.

### How It Works

The `expect_file_chooser()` context manager:

1. Enables file chooser interception
2. Waits for the file chooser dialog to open
3. Automatically sets the files when the dialog appears
4. Cleans up after the operation completes

### Basic Usage

```python
import asyncio
import os
from pydoll.browser.chromium import Chrome

async def file_chooser_upload():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/custom-upload')
        
        # Prepare the file path
        file_path = os.path.join(os.getcwd(), 'document.pdf')
        
        # Use context manager to handle file chooser
        async with tab.expect_file_chooser(files=file_path):
            # Click the custom upload button
            upload_button = await tab.find(class_name='custom-upload-btn')
            await upload_button.click()
            # File is automatically set when dialog opens
        
        # Continue with your automation
        print("File selected via chooser!")

asyncio.run(file_chooser_upload())
```

### Multiple Files with File Chooser

```python
import asyncio
from pydoll.browser.chromium import Chrome

async def multiple_files_chooser():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/gallery-upload')
        
        # Prepare multiple files
        files = [
            '/home/user/photos/img1.jpg',
            '/home/user/photos/img2.jpg',
            '/home/user/photos/img3.jpg'
        ]
        
        async with tab.expect_file_chooser(files=files):
            # Trigger upload via custom button
            add_photos_btn = await tab.find(text='Add Photos')
            await add_photos_btn.click()
        
        print(f"{len(files)} files selected!")

asyncio.run(multiple_files_chooser())
```

### Dynamic File Selection

```python
import asyncio
import glob
from pydoll.browser.chromium import Chrome

async def dynamic_file_selection():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/batch-upload')
        
        # Find all CSV files in a directory
        csv_files = glob.glob('data/*.csv')
        
        async with tab.expect_file_chooser(files=csv_files):
            upload_area = await tab.find(class_name='drop-zone')
            await upload_area.click()
        
        print(f"Selected {len(csv_files)} CSV files")

asyncio.run(dynamic_file_selection())
```

!!! tip "When to Use File Chooser"
    Use `expect_file_chooser()` when:
    
    - The file input is hidden or not directly accessible
    - Custom buttons trigger the file chooser dialog
    - Working with drag-and-drop upload areas
    - The site uses JavaScript to open file dialogs

## Comparison: Direct vs File Chooser

| Feature | `set_input_files()` | `expect_file_chooser()` |
|---------|---------------------|-------------------------|
| **Speed** | ⚡ Instant | 🕐 Waits for dialog |
| **Complexity** | Simple | Requires context manager |
| **Requirements** | Visible file input | Any upload trigger |
| **Use Case** | Standard forms | Custom upload UIs |
| **Event Handling** | Not needed | Uses page events |

## Complete Example

Here's a comprehensive example combining both approaches:

```python
import asyncio
import os
from pathlib import Path
from pydoll.browser.chromium import Chrome

async def comprehensive_upload_example():
    async with Chrome() as browser:
        tab = await browser.start()
        await tab.go_to('https://example.com/upload-form')
        
        # Scenario 1: Direct input for profile picture
        avatar_input = await tab.find(id='avatar-upload')
        avatar_path = Path.home() / 'Pictures' / 'profile.jpg'
        await avatar_input.set_input_files([str(avatar_path)])
        
        # Wait a bit for preview to load
        await asyncio.sleep(1)
        
        # Scenario 2: File chooser for document upload
        document_path = os.path.join(os.getcwd(), 'documents', 'resume.pdf')
        async with tab.expect_file_chooser(files=document_path):
            # Custom styled button that triggers file chooser
            upload_btn = await tab.find(class_name='btn-upload-document')
            await upload_btn.click()
        
        # Wait for upload confirmation
        await asyncio.sleep(2)
        
        # Scenario 3: Multiple files via file chooser
        certificates = [
            'certs/certificate1.pdf',
            'certs/certificate2.pdf',
            'certs/certificate3.pdf'
        ]
        async with tab.expect_file_chooser(files=certificates):
            add_certs_btn = await tab.find(text='Add Certificates')
            await add_certs_btn.click()
        
        # Submit the complete form
        submit_button = await tab.find(type='submit')
        await submit_button.click()
        
        # Wait for success message
        success_msg = await tab.find(class_name='success-message', timeout=10)
        message_text = await success_msg.text
        print(f"Upload result: {message_text}")

asyncio.run(comprehensive_upload_example())
```

## Learn More

For deeper understanding of the file upload mechanisms:

- **[Event System](../advanced/event-system.md)**: Learn about the page events used by `expect_file_chooser()`
- **[Deep Dive: Tab Domain](../../deep-dive/tab-domain.md#file-chooser-handling)**: Technical details on file chooser interception
- **[Deep Dive: Event System](../../deep-dive/event-system.md#file-chooser-events)**: How file chooser events work under the hood

File operations in Pydoll eliminate one of the biggest pain points in browser automation, providing clean, reliable methods for both simple and complex upload scenarios.
