# A Bit of A and I

Our blog where we share our research and projects.

## About

This repository contains the source code for our blog "A Bit of A and I", hosted on GitHub Pages. Visit our blog at [https://necou.github.io](https://necou.github.io).

## Blog Structure

The blog is organized as follows:

- `index.html`: The home page of the blog
- `styles.css`: Main stylesheet for the blog
- `posts/`: Directory containing all blog posts
  - `multimodal-fusion.html`: Our first blog post on optimizing multimodal fusion

## Adding a New Blog Post

To add a new blog post:

1. Create a new HTML file in the `posts/` directory with a descriptive name (e.g., `new-research-topic.html`)
2. Copy the structure from an existing blog post template
3. Update the content with your new blog post
4. Add a preview of the new post to `index.html` by adding a new `<article class="post-preview">` element

Example of a new post preview to add to `index.html`:

```html
<article class="post-preview">
    <h2><a href="posts/new-research-topic.html">Title of Your New Blog Post</a></h2>
    <div class="post-meta">
        <span class="post-date">Publication Date</span>
    </div>
    <p class="post-excerpt">
        A brief excerpt or summary of your new blog post.
    </p>
    <a href="posts/new-research-topic.html" class="read-more">Read More</a>
</article>
```

## Development

To test the blog locally before pushing changes:

1. Clone the repository
2. Open the `index.html` file in your browser
3. Make your changes
4. Refresh the browser to see the changes

## Links

- [Our GitHub Repository](https://github.com/NECOU)
- [Our Work on Hugging Face](https://huggingface.co/NECOUDBFM/Jellyfish)
