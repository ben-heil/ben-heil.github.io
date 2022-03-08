---
layout: post
title: How to Use Font Awesome Icons in Streamlit
description: "Two ways to use Font Awesome icons in a Streamlit app"
cover-img: "/assets/img/traintracks.jpg"
tags: ['data viz', 'font awesome', 'streamlit']
---
## Intro

[Font Awesome](https://fontawesome.com/) provides icons for making websites look better that are used by [hundreds of thousands of projects](https://github.com/FortAwesome/Font-Awesome/network/dependents?package_id=UGFja2FnZS00Njc2MTc0NTU%3D). 
If you have used the icons in the past, or found an icon that would fit well in your project, you might find yourself wanting to use them in a [Streamlit](https://streamlit.io/) web app.

Unfortunately, until now there hasn't been a guide on how to make Font Awesome and Streamlit work together.
[Font Awesome's docs](https://fontawesome.com/docs/web/setup/get-started) assume you're working in a world with `<script>` tags readily available, which isn't the case in Streamlit.
While you can add HTML and CSS to the markdown used by Streamlit, scripts are not run.

Despite these challenges, there are still two methods to use Font Awesome icons in your streamlit app: a preferred method using CSS, and a backup method using Streamlit Components.
Example code showing how each method can be used (and for generating the images in this post) [can be found here](https://github.com/ben-heil/streamlit_awesome).

## Import Icons with CSS
``` html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
```

<a id='component'></a>
## Using a Streamlit HTML Component
