# website

This is my personal website built with Jekyll, hosted on GitHub Pages. Visit it at [danielchen0.github.io](https://danielchen0.github.io).

## Project Structure

- `index.html` - Main landing page
- `blog.markdown` - Blog page showing all posts
- `_posts/` - Directory containing blog posts
- `css/` - Stylesheets
- `_includes/` - Reusable components like navigation

## Local Development

### Prerequisites

- Ruby
- Bundler (Ruby package manager): `sudo gem install bundler`
- Jekyll (~> 4.2.1)

### Setup

1. Clone the repository:
```bash
git clone https://github.com/danielchen0/danielchen0.github.io.git
cd danielchen0.github.io
```

2. Install dependencies:
```bash
bundle install
```

3. Run the development server:
```bash
bundle exec jekyll serve
```

4. Visit [http://localhost:4000](http://localhost:4000) in your browser

### Bundle/Platform Issues

If you encounter platform-specific issues with Bundler, you may need to add your platform:

```bash
bundle lock --add-platform x86_64-linux
bundle lock --add-platform x86_64-darwin
```

### Dependencies

Key dependencies from our Gemfile:
- jekyll (~> 4.2.1)
- minima (~> 2.5) - Default theme
- jekyll-feed (~> 0.12) - RSS feed generation
- webrick (~> 1.7) - HTTP server toolkit

## Deployment

The site is automatically built and deployed to GitHub Pages when changes are pushed to the main branch.

## License

All rights reserved. The content and code in this repository may not be used without permission. 
