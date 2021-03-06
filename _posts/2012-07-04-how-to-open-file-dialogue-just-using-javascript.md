---
layout: post
title:  How to open file dialogue just using JavaScript
date:   2012-07-04 17:00:12 +0000
permalink: /top-tips/how-to-open-file-dialogue-just-using-javascript
category: top-tips
meta_description: >
 Open file select dialogue just using javascript or jquery and make it capable of selecting multiple files.
---

Say for instance that you want to open the_ file select dialogue_ for a user to select an file to upload. Like a photo, pdf or any other file type. But you don't want to use the standard file input HTML element but instead use a link or button to show the file window.

You might have tried hiding the file select element on the page and then running the click event when the link is clicked. This does not work if you use the CSS property visibility : hidden you need to use display:none for the click event to work and show the dialogue. The issue with having the file select element in the html is when you try to reselect more files, it does not clear the file data correctly. You will end up having strange errors and the only way to fix this is to recreate the input file element by removing it and re-adding it. By using this JavaScript method you won't have this issue as you don't add it to the HTML code.

### Here is an example just using standard JavaScript with a normal link:

{% highlight javascript %}
var fileSelector = document.createElement('input');
fileSelector.setAttribute('type', 'file');

var selectDialogueLink = document.createElement('a');
selectDialogueLink.setAttribute('href', '');
selectDialogueLink.innerText = "Select File";

selectDialogueLink.onclick = function () {
     fileSelector.click();
     return false;
}

document.body.appendChild(selectDialogueLink);
{% endhighlight %}

### An example using jQuery:

{% highlight javascript %}
var selectDialogueLink = $('<a href="">Select files</a>');
var fileSelector = $('<input type="file">');

selectDialogueLink.click(function(){
    fileSelector.click();
    return false;
});
$('body').html(selectDialogueLink);
{% endhighlight %}

This can be further enhanced by making the file dialogue capable of selecting multiple files. This is an HTML 5 so its not available in older browsers. See **examples **below:

{% highlight javascript %}
//JavaScript add in the line
fileSelector.setAttribute('multiple', 'multiple');

//jQuery - change the file select
var fileSelector = $('<input type="file" multiple="">');
{% endhighlight %}

Now you can open a file select dialogue without the file element being present in the html.

The next step is how to get the file information out ready to upload. I will explain in my next post, getting&nbsp;and [preview selected images&nbsp;using JavaScript][1].

[1]: /top-tips/preview-selected-images-for-uploading