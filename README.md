<p align="center">
    <h1>Pydoll: Async Web Automation in Python!</h1>
</p>
<br>
<p align="center">
    <img src="https://github.com/user-attachments/assets/c4615101-d932-4e79-8a08-f50fbc686e3b" alt="Alt text" /> <br><br>
</p>

<p align="center">
    <a href="https://codecov.io/gh/autoscrape-labs/pydoll">
        <img src="https://codecov.io/gh/autoscrape-labs/pydoll/graph/badge.svg?token=40I938OGM9"/> 
    </a>
    <img src="https://github.com/thalissonvs/pydoll/actions/workflows/tests.yml/badge.svg" alt="Tests">
    <img src="https://github.com/thalissonvs/pydoll/actions/workflows/ruff-ci.yml/badge.svg" alt="Ruff CI">
    <img src="https://github.com/thalissonvs/pydoll/actions/workflows/release.yml/badge.svg" alt="Release">
    <img src="https://tokei.rs/b1/github/thalissonvs/pydoll" alt="Total lines">
    <img src="https://tokei.rs/b1/github/thalissonvs/pydoll?category=files" alt="Files">
    <img src="https://tokei.rs/b1/github/thalissonvs/pydoll?category=comments" alt="Comments">
</p>

<p align="center">
  <b>Pydoll</b> is revolutionizing browser automation! Unlike other solutions, it <b>eliminates the need for webdrivers</b>, 
  providing a smooth and reliable automation experience with native asynchronous performance.
</p>

<p align="center">
  <a href="https://autoscrape-labs.github.io/pydoll/">Documentation</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-contributing">Contributing</a> •
  <a href="#-support-my-work">Support</a> •
  <a href="#-license">License</a>
</p>

## Big Updates Are in the Works!

I'm working on a major update for **pydoll**, bringing several improvements and exciting new features. This version introduces some breaking changes, and I'm taking time to make things cleaner, faster, and easier to maintain.

### What's changing?
- Refactored module and file structure for better clarity and maintainability
- Improved logging system for easier debugging and issue tracking
- Static typing support using `mypy`
- A modern, dynamic way to query elements (moving away from the Selenium-style approach)

### New Features Coming
- `connect_to_remote` to support remote browser connections
- `set_browser_prefs` in the `Options` class to more concise and pragmatic Chrome configuration
- Shadow DOM interaction support
- HTML extension interface interaction
- Improved click system (hover, drag, move – like `ActionChains`)
- Dynamic proxy switching (no need to restart browser)

### What to Expect
This is a big shift and may take a bit of time. While v2 is in progress:
- Releases may slow down temporarily
- Focus will shift to bugfixes and issue responses

Thanks for your continued support, and stay tuned!

## Key Features

🔹 **Zero Webdrivers!** Say goodbye to webdriver compatibility nightmares  
🔹 **Native Captcha Bypass!** Smoothly handles Cloudflare Turnstile and reCAPTCHA v3*  
🔹 **Async Performance** for lightning-fast automation  
🔹 **Human-like Interactions** that mimic real user behavior  
🔹 **Powerful Event System** for reactive automations  
🔹 **Multi-browser Support** including Chrome and Edge

## Installation

```bash
pip install pydoll-python
```

## Quick Start

Get started with just a few lines of code:

```python
import asyncio
from pydoll.browser.chrome import Chrome
from pydoll.constants import By

async def main():
    async with Chrome() as browser:
        await browser.start()
        page = await browser.get_page()
        
        # Works with captcha-protected sites
        await page.go_to('https://example-with-cloudflare.com')
        button = await page.find_element(By.CSS_SELECTOR, 'button')
        await button.click()

asyncio.run(main())
```

Need to configure your browser? Easy!

```python
from pydoll.browser.chrome import Chrome
from pydoll.browser.options import Options

options = Options()
# Add a proxy
options.add_argument('--proxy-server=username:password@ip:port')
# Custom browser location
options.binary_location = '/path/to/your/browser'

async with Chrome(options=options) as browser:
    await browser.start()
    # Your code here
```

## Documentation

For comprehensive documentation, examples, and deep dives into Pydoll's features, visit our [official documentation site](https://autoscrape-labs.github.io/pydoll/).

The documentation includes:
- Detailed usage examples
- API reference
- Advanced techniques and patterns
- Troubleshooting guides

## Sponsors

[CapSolver](https://www.capsolver.com/?utm_source=github&utm_medium=banner_repo&utm_campaign=scraping&utm_term=pydoll) is an AI-powered tool that easily bypasses Captchas, allowing uninterrupted access to public data with fast, reliable, and cost-effective. And please enjoy the code PYDOLL to get an extra 6% balance! You can register [here](https://dashboard.capsolver.com/passport/?utm_source=github&utm_medium=banner_repo&utm_campaign=scraping&utm_term=pydoll).

<p align="left">
  <a href="https://www.capsolver.com/?utm_source=github&utm_medium=banner_repo&utm_campaign=scraping&utm_term=pydoll" target="_blank">
    <img src="https://github.com/user-attachments/assets/aaf49563-2b93-49c3-8f9c-c2dccc8dc0c8" alt="Pydoll Sponsors" width="1200" />
  </a>
</p>

Pydoll is proudly supported by these amazing sponsors who believe in the future of webdriver-free automation. Their contributions make it possible for us to maintain and improve this project.
Interested in becoming a sponsor? Check out our [GitHub Sponsors page](https://github.com/sponsors/thalissonvs) for more information about the perks and benefits of sponsoring this project!

## Contributing

We'd love your help making Pydoll even better! Check out our [contribution guidelines](CONTRIBUTING.md) to get started. Whether it's fixing bugs, adding features, or improving documentation - all contributions are welcome!

Please make sure to:
- Write tests for new features or bug fixes
- Follow coding style and conventions
- Use conventional commits for pull requests
- Run lint and test checks before submitting

## Support My Work

If you find my projects helpful, consider [sponsoring me on GitHub](https://github.com/sponsors/thalissonvs).  
You'll get access to exclusive perks like prioritized support, custom features, and more!

Can't sponsor right now? No problem — you can still help a lot by:
- ⭐ Starring the repo
- 🐦 Sharing it on social media
- 📝 Writing blog posts or tutorials
- 💬 Giving feedback or reporting issues

Every bit of support makes a difference — thank you! 🙌

## 📄 License

Pydoll is licensed under the [MIT License](LICENSE).

---

<p align="center">
  <b>Pydoll</b> — Making browser automation magical! ✨
</p>
