# A Bit of A and I

Our blog where we share our research and projects in AI.

## About This Repository

This repository contains the source for our Jekyll-based blog hosted on GitHub Pages.
Visit our blog at [https://necou.github.io](https://necou.github.io).

## Blog Structure

- `_config.yml`: Jekyll configuration file
- `_posts/`: Directory containing all blog posts in Markdown format
- `index.md`: Home page
- `about.md`: About page

## Writing a New Post

1. Create a new Markdown file in the `_posts` directory with the naming convention: `YYYY-MM-DD-title-with-hyphens.md`
2. Add the YAML front matter at the top of your file:
```yaml
---
layout: post
title: "Your Post Title Here"
date: YYYY-MM-DD
categories: research
---
```
3. Write your content in Markdown format
4. Commit and push to GitHub

## Local Development

To test your blog locally:

1. Install Ruby and Jekyll: `gem install jekyll bundler`
2. Clone this repository
3. Run `bundle install`
4. Start the local server: `bundle exec jekyll serve`
5. View your site at `http://localhost:4000`

## Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Markdown Guide](https://www.markdownguide.org/)

## Links

- [Our GitHub Repository](https://github.com/NECOU)
- [Our Work on Hugging Face](https://huggingface.co/NECOUDBFM/Jellyfish)