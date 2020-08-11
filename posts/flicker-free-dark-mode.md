---
title: Flicker free dark mode toggle
date: 2020-08-11
---
There are tons of articles on the internet about dark mode and this is not one of them. This is simply a log of how I achieved a flicker free dark mode toggle that you see on this site.

So how do you implement a dark-mode toggle? We'll need three things:

1. CSS that defines colors for "light" and "dark" modes.
2. HTML to indicate the current color mode.
3. A button to toggle between the two.

## 1. CSS

CSS variables give us the flexibility to create styles that can be easily switched. The method I follow here is to define colors in variables that I can simply switch when needed.

Let's start be defining two `root` styles:

```css
:root[color="light"] {
    --background-color: #fbfffe;
    --text-color: #001514;
    --link-color: #2F4858;
    --link-hover-color: #001514;
}

:root[color="dark"] {
    --background-color: #2d2d2d;
    --text-color: #C5C5C5;
    --link-color: #ACA9BB;
    --link-hover-color: #C5C5C5;
}
```

The styles are scoped to the `color` arrtibute, what this means is that any element that has this attribute set to either "light" or "dark" will get the appropriate colors.

The question now is where do we stick this attribute? Why, the `documentElement` ofcourse. This simply means the root `<html>` tag.

## 2. Markup

```html
<html color="light">
    ...
    ...
</html>
```

-or-

```html
<html color="dark">
    ...
    ...
</html>
```

Great! Now that we have tackled the markup and the styling its time to move on to the toggle button.

Our button is going to be a `<div id="dark-mode-toggle">` with an `<svg>` icon inside it, styled like a button i.e., the cursor changes to a `pointer` to indicate that this is clickable. Go ahead, try clicking the icon you see at the top right corner of this page.

## 3. JavaScript

Now the fun bits, handling the click event and doing something:

```js
document.querySelector("#dark-mode-toggle").addEventListener("click", function() {
    var color = document.documentElement.getAttribute("color") === "light" ? "dark" : "light";
    document.documentElement.setAttribute("color", color);
});
```

That's it!

Well, not really. How do you persist this across page loads?

Since we are in `SSG` land, `localStorage` is our best option. Let's implement that (yay! more JS). Here is the complete implementation:

```js
if (window.localStorage.getItem("color") === "dark") {
    document.documentElement.setAttribute("color", "dark");
} else {
    document.documentElement.setAttribute("color", "light");
}

window.addEventListener("load", function(e) {
    document.querySelector("#dark-mode-toggle").addEventListener("click", function() {
        var color = document.documentElement.getAttribute("color") === "light" ? "dark" : "light";
        document.documentElement.setAttribute("color", color);
        window.localStorage.setItem("color", color);
    });
});
```

Cool. But is it flicker free? Have a go at it then, click that toggle on the top-right hand corner and visit various pages in this site and see if the page loads keep up with the setting.

## Credits

Well, people way smarter that me have already tackled this, I am mearly inspired by them. Hope you are too.

1. [https://ryanfeigenbaum.com/dark-mode/](https://ryanfeigenbaum.com/dark-mode/)
2. [https://css-tricks.com/lets-say-you-were-going-to-write-a-blog-post-about-dark-mode/](https://css-tricks.com/lets-say-you-were-going-to-write-a-blog-post-about-dark-mode/)

Cheers!
