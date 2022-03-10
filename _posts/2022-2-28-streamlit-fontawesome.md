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
The first way to get Font Awesome icons into your app is to avoid using script tags altogether by importing Font Awesome's css from an existing library.
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
<!-- empty paragraph to keep the image and code from crowing each other -->
<p></p>
{% include responsive-image.html image="/assets/img/post_img/css_fa_icons.png" %}

While this method works well, it's not the safest.
As with all instances of using code from an external source, we're trusting the CDNJS people not to upload vulnerable or malicious code.
Additionally, since we're using adding HTML tags to our markdown, we have to use the `unsafe_allow_html` parameter in Streamlit.
This can potentially leave us open to HTML injection attacks (see [this discussion](https://discuss.streamlit.io/t/why-is-using-html-unsafe/4863/4) for more details).

For my uses, the risks posed by pulling font-awesome from a website are acceptable, but your own appetite for risk my vary.

<a id='component'></a>
## Method 2: Using a Streamlit HTML Component
If you want a more secure method for using Font Awesome icons (or HTML in general), a Streamlit [HTML Component](https://docs.streamlit.io/1.6.0/library/components/components-api) is probably the right choice for you.
Using HTML Components puts any scripts you want to run in an [IFrame](https://www.verygoodsecurity.com/blog/posts/iframes-as-a-security-feature) to isolate them from the rest of your Streamlit app.
This method gives you much more freedom to include whatever you want in your app, but it's not without its drawbacks.

Because your component is isolated from the rest of the app, it doesn't inherit its CSS.
As a result, you'll have to put in some work (and CSS) to make whatever lives in your component have the same style as everything else.
The other main drawback of an HTML Component is that it's in HTML.
While the rest of your app's text is written and styled in Markdown, whatever lives in the Component will have to be written in HTML.

There are good reasons to use an HTML Component though. 
For example, if you want to use paid icons you'll have to go the Component route.
Alternatively, if you have a [Font Awesome kit](https://fontawesome.com/docs/web/setup/use-kit) you want to use, you'll need to use script tags and by extension HTML Components.

If you decide to go the Component route, here's example code to get you started:

``` html
<!-- awesome.html -->
<script src="https://kit.fontawesome.com/2c74303849.js" crossorigin="anonymous"></script>
<p> I'm in an HTML component containing fontawesome icons from a kit! I don't inherit CSS automatically!</p>
<p>
<i class="fa-solid fa-square"></i>
<i class="fa-solid fa-dragon"></i>
<i class="fa-solid fa-paw"></i>
</p>
```

``` python
import streamlit as st


def load_text(file_path):
    """A convenience function for reading in the files used for the site's text"""
    with open(file_path) as in_file:
        return in_file.read()
                                                                                
                                                                                
if __name__ == '__main__':                                                      
    # Other demo code omitted for clarity

    st.write('### Component Example')
    # HTML component
    component_example = load_text('awesome.html')
    st.components.v1.html(component_example)
```
<!-- empty paragraph to keep the image and code from crowing each other -->
<p></p>
{% include responsive-image.html image="/assets/img/post_img/html_fa_icons.png" %}

## Conclusion
Hopefully you will now be able to use Font Awesome icons on your Streamlit app to make your fonts more awesome and your app more lit.
Let me know if you know of workarounds for styling in HTML Components, or if you have another method you prefer to use to get icons in your app.
