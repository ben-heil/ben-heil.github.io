---
layout: post
title: How to Use Font Awesome Icons in Streamlit
description: "Two ways to use Font Awesome icons in a Streamlit app"
cover-img: "/assets/img/traintracks.jpg"
tags: ['data viz', 'font awesome', 'streamlit']
---
## Intro

[Font Awesome](https://fontawesome.com/) provides website icons used by [hundreds of thousands of projects](https://github.com/FortAwesome/Font-Awesome/network/dependents?package_id=UGFja2FnZS00Njc2MTc0NTU%3D). 
If you have used the icons in the past, or found an icon that would fit well in your project, you might find yourself wanting to use them in a [Streamlit](https://streamlit.io/) web app.

Unfortunately, until now there hasn't been a guide on how to make Font Awesome and Streamlit work together.
[Font Awesome's docs](https://fontawesome.com/docs/web/setup/get-started) assume you're working in an environment with `<script>` tags readily available, which isn't the case in Streamlit.
While you can add HTML and CSS to the markdown used by Streamlit, scripts are not run.

Despite these challenges, there are still two methods to use Font Awesome icons in your streamlit app: a preferred method using CSS, and a backup method using Streamlit Components.
Example code showing how each method can be used (and for generating the images in this post) [can be found here](https://github.com/ben-heil/streamlit_awesome).

## Method 1: Import Icons with CSS
The first way to get Font Awesome icons into your app is to avoid using `script` tags altogether by importing Font Awesome's css from an existing library.
In the example below, we use a version stored in the [cdnjs](https://cdnjs.com/).
``` python
import streamlit as st
css_example = '''
I'm importing the font-awesome icons as a stylesheet!                                                                                                                                                       
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">                                                                                                    
                                                                                                                                                                                                            
<i class="fa-solid fa-square"></i>                                                                                                                                                                          
<i class="fa-solid fa-dragon"></i>                                                                                                                                                                          
<i class="fa-solid fa-paw"></i> 
'''
st.write(css_example, unsafe_allow_html=True)
```
TODO insert image

While this method works well, it's not the safest.
As with all instances of using code from an external source, we're trusting the CDNJS people not to upload vulnerable or malicious code instead.
Additionally, since we're using adding HTML tags to our markdown, we have to use the `unsafe_allow_html` parameter in Streamlit.
This can potentially leave us open to HTML injection attacks (see [this discussion](https://discuss.streamlit.io/t/why-is-using-html-unsafe/4863/4) for more details).

For my uses, the risks posed by pulling font-awesome from a website are acceptable, but your own appetite for risk my vary.

<a id='component'></a>
## Method 2: Using a Streamlit HTML Component
