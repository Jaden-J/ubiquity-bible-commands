If you are unfamiliar with Ubiquity Please first check it out   [here](http://labs.mozilla.com/2008/08/introducing-ubiquity/). Watch the video to get an idea of what it is all about.

**The First Goal**

_Verse references to links_

**I type some verse references in an email, say John 3:16, 1 Timothy 4:12, Matt. 5 and... Titus 2:11.** Next I highlight them and execute an ubiquity command on them.
**for example, ubiq "bible-link"** this then links each reference to where it is located at biblegateway.com
So, the code would do the following:

  1. Find the verse references in the selected text (the hard part I think)
  1. Create the hyperlinks from verses: i.e. "http://www.biblegateway.com/passage/?search=Titus%202:2-3;&version=31;"
  1. Links the emails to the pages at biblegateway

**The Second Goal**

_Say I select Genesis 1:1 and execute "verse" command (or whatever we want to call it). It could convert it to the actual bible verse._

  1. Turning the reference to a URL...  puts "Genesis 1:1" into this:  http://www.biblegateway.com/passage/?search=Genesis%201:1;&version=31;
  1. Parsing the HTML from [biblegateway.com biblegateway.com]
  1. Extracting the verse and replacing the reference with it.
  1. From Genesis 1:1 to "In the beginning God created the heavens and the earth." in a few key strokes

To code for this, check out the Ubiquity command [Authoring Tutorial](https://wiki.mozilla.org/Labs/Ubiquity/Ubiquity_0.1_Author_Tutorial).

We can use the "bible-ref" command (found via "herd") that Chris made as a base, check it out:

```
makeSearchCommand({
  name: "Bible-ref",
  url: "http://www.biblegateway.com/passage/?search={QUERY}",
  icon: "http://biblegateway.com/favicon.ico",
  author: {name: "Chris Lohroff", email: "chris@lohroff.com"}, 
  description: "Displays the highlighted Bible reference"
});
```

Bye for now! Check out the downloads tab to grab a frst bit of code to play with. ubiq "command-editor" and paste it in.

_**For the furtherance of the gospel...**_

Nathan

Updates:

The Latest that I've been working on:

```
CmdUtils.CreateCommand({
  name : "verse-link",
  takes : {"verse reference" : noun_arb_text},
  icon: "http://www.biblegateway.com/favicon.ico",
  description: "Links it to biblegateway verse",
  help: "Can only be used in a rich text-editing field.",
    preview: function(pblock, directObject) {
    
    searchText = jQuery.trim(directObject.text);
    if(searchText.length < 1) {
      pblock.innerHTML = "Searches for bible verse";
      return;
    }

    var previewTemplate = "Links <b>${query}</b> to bible gateway reference.";
    var previewData = {query: searchText};
    pblock.innerHTML = CmdUtils.renderTemplate(previewTemplate, previewData);

  },
  execute : function( directObj ){
    var text = directObj.text;
    var verse = text.replace(/ /g, "%20");
    var html = ("<a href=\"http://www.biblegateway.com/passage/?search="
                + verse + ";&version=47;" + "\">" + text + "</a>");

    var doc = context.focusedWindow.document;
    if (doc.designMode == "on")
      doc.execCommand("insertHTML", false, html);
    else
      displayMessage("You're not in a rich text editing field.");
  },
});

CmdUtils.CreateCommand({
  name: "verse-goto",
  icon: "http://www.biblegateway.com/favicon.ico",
  takes : {"verse reference" : noun_arb_text},
  preview: function(pblock, directObject) {
    
    searchText = jQuery.trim(directObject.text);
    if(searchText.length < 1) {
      pblock.innerHTML = "Searches for bible verse";
      return;
    }

    var previewTemplate = "Opens <b>${query}</b> in new tab.";
    var previewData = {query: searchText};
    pblock.innerHTML = CmdUtils.renderTemplate(previewTemplate, previewData);

  },
  execute: function(directObject) {
    var url = "http://www.biblegateway.com/passage/?search={QUERY}"
    var query = directObject.text;
    var urlString = url.replace("{QUERY}", query);
    Utils.openUrlInBrowser(urlString);
  }
});

CmdUtils.CreateCommand({
  name: "todays-verse",
  icon: "http://www.biblegateway.com/favicon.ico",
  preview: "Takes you to Bible Gateways verse of the day Atom feed",
  execute: function(){
  var url = "http://www.biblegateway.com/votd/get/?format=atom";
  Utils.openUrlInBrowser(url);
}
}}
```