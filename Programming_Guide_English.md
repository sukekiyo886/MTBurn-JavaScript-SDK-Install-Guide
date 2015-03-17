# In-Feed Advertisement
 
# Getting Started

- Enter the template of ads unit on `head` tag.
Assign the URL of creative material. Insert position of ads text at placeholder.
At introducing In-Feed-Ads, add the wording to disclose that the frame adjacent to ad spots is an advertisement to users (ex. PR or Sponsored)

```html
<script type="text/advs-instream-template" data-adspot-id="MjQzOjIw">
<div class="article">
 <div class="icon">
   <a href="{{click_url}}">
     <img src="{{main_image_url}}" />
   </a>
 </div>
 
 <div class="contents">
   <h3>{{title}}</h3>
   <p>【PR】{{description}}</p>
   <span class="source">Sponsored</span>
 </div>
</div>
</script>
```

- Create appropriate empty element on ads display position. 
Assign the ad spot ID advancely given by `data-advs-adspot-id` element. 
This element will be replaced with DOM of ads.

```html
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

- Call for script for ads.

```html
<script src="http://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run();
</script>
```

## Registration of Establishment of ad spots
 
 
Register ad spots in advance. 
ad spots ID will be given by setting information below.
 
- Name of ad spots
- Size of ads image
- The number of ads
- Site URL (Store URL for application)

Register ad spots for each displayed format.

## template of ads unit
 
Describe the DOM structure of ads unit on template. Assign the insert position of ads at placeholder.
Describe placeholder using notation `{{parameter_name}}`

About available parameters, please refer to  
https://github.com/mtburn/MTBurn-JavaScript-SDK-Install-Guide/blob/master/Programming_Guide_English.md
  
## Designation of the ads insert position

Create appropriate empty element at ads insert position. 
Assign the ad spot ID given by `data-advs-adspot-id` element. 
This element will be replaced with the content of ads when loaded.
  
Assign `display:none` style as element in order not to affect the website display in case of loading failure.

```
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

## Loading adsvertisement

Call for JavaScript code of SDK for loading and processing. 
Acquire and display ads contentcases after the load of display is completed.

```
<script src="http://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run();
</script>
```

# Additional load of ads

Additional load of ads is available when user arrives the lower part of website and read extra feed (UI) on feed-typed website.

- Include designating element of ad position, which is the same as initial state on DOM inserted when reading extra feed.

```
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

- Call for `MTBADVS.InStream.Default.reloadAds()` method of SDK after reading extra feed. 
Acquire and display ads cases after calling.

```
// Example of function called after reading extra feed
function onAdditionalFeedLoaded() {
  // ...

  // Call for “reloadAds” method of SDK
  MTBADVS.InStream.Default.reloadAds();
}
```

# Shortening of the title of ads and description

Adding the option when ads are called enables to shorten the title of ads and description depending on your website.  
This below is an example for description, which has up to 30 letters.

```
MTBADVS.InStream.Default.run({
   description_length: 30
});
```

Latter part of text will be cut off in order to make the description within 30 letters, 
and ellipsesthree dots leader `...` will be automatically added at the end of sentence. 
Whole description will be adjusted to make the description within 30 letters including ellipsesthree dots leader.  
  
These options below are capable to be assigned.
 
| Name of option | Description | Example of setting | Example of operation result |
|---|---|---|---|
| title_length | Assign the maximum length of title | `5` | `This is title` -> `This...`|
| description_length | Assign the maximum length of description | `10` | `This is description` -> `This is des...`|  

# Editing by callback functions

Giving the callback functions allows you to adjust the length of the string in your optional manner. 
Callback functions will be called right before displaying ads. 
The content of ads and location will be given as an argument. 
At this point, you can describe the process to edit the content. 
And also, you can edit the number of characters of item according to the position. 
Callback functions should be given to `before_render` option.  
  
The following, place `[PR]` at the head of title. 
The number of characters of ad spots A should be within 30, 
and ad spots B should be within 40 including ellipses at the end of sentence.

```
MTBADVS.InStream.Default.run({
  before_render: function(ad_info, placement_info) {

    // Insert `[PR]` at the head of the title.
    ad_info.title = '[PR] ' + ad_info.title;

    // Assign the maximum length of description for each ad spots.
    var desc_max_len = 30;
    if (placement_info.adspot_id === 'ADSPOT_A') {
      desc_max_len = 30;
    } else if (placement_info.adspot_id === 'ADSPOT_B') {
      desc_max_len = 40;
    }

    // Keep the number of characters of description within maximum length, and add ellipses at the end of text
    if (ad_info.description.length > desc_max_len) {
      ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
    }

    // Return the edit result
    return ad_info;
  }
});
```

- Object of ads content will be given for the first argument of `before_render`. 
Please refer to [ads parameter for object’s property]().

- Object of placement position will be given for the second argument of `before_render`. 
Object’s property is `adspot_id` (ad spots ID)

- As for callback functions, the edited contents of ads object should necessarily be given back by `return`.

# Customized implementation

Using `raw API` of JavaScript SDK, calling and rendering ads are available in the intended timing. 
Using the following explanation of API, extra implementation is needed.

# Create an instance

Create an instance of controller class to manage ads. 
Assigning ad spots ID is necessary to have constructor.

```
var ad_controller = new MTBADVS.InStream.AdController({ adspot_id: 'MjQzOjIw' });
```

# Sending ads request

Sending request of ads contents, and then get the information by using `loadAds()` method. 
Set down callback functions showed as argument after completed.  
  
The First argument of callback functions is returned error object. 
If it works well, it returns `null`.

```html
var on_ad_loaded = function(error) {
  if (error) {
    // error handling
  }

  // ...
};

ad_controller.loadAds(on_ad_loaded);
``` 

# Receiving ads content

It is possible to receive ads contents which was got from server by using `loadAds()` by using `getLoadedAds()` method.

```html
var ads = ad_controller.getLoadedAds();
```

A response value of `getLoadedAds()` is as below.

```html
[
  {
    "title": "testAd",
   	"description": "This is test ad.”
   	"click_url": "http://...",
   	"main_image_url": "http://...",
   	"icon_image_url": "http://...",
   	"ad_id": 123,
  },
  ...
]
```

If you want to know about more details of each parameter, please refer to [clause of ads parameter]().

# Adsvertisement display

You can show ads recieved by using `getLoadedAds()`. It needs to manage display processing.

# Sending notification of ads impression

After showing ads, it is necessary to notice impression by using `notifylmp()` and passing `ad_id` as parameter.

```html 
var ad_id = ads[0].ad_id;
ad_controller.notifyImp(ad_id);
```

# Additional Loading
 
You can load more ads contents in case of reloaded feed.  
  
Every time calling for `loadAd()` method, you can get new ads content. 
`GetLoadedAds()` returns all of ads had been got so far.  
  
It needs to use an controller instance, at first. 
If you keep using the only one instance, duplicate advertisements are never returned.

## Example of implementation

```html
var ad_controller = new MTBADVS.InStream.AdController({ adspot_id: 'MjQzOjIw' });

var on_ad_loaded = function(error) {
  if (error) {
    // Error handling
  }

  // Recieving
  var ads = ad_controller.getLoadedAds();

  ads.forEach(function(ad) {
    // showing ads according to ads content
    ShowAd(ad);

    // Notification of Ads impression
    var ad_id = ad.ad_id;
    ad_controller.notifyImp(ad_id);
  });
};

ad_controller.loadAds(on_ad_loaded);
```

### Attention

- Every time reading pages, get ads contents from server. 
Please refrain from keep using cash that you had got.
- Ads contents you get from server are arranged to profitable order. 
It is better to show superior ads at the top.

## Adsvertisement parameter

| Parameter name | Explanation | Example |
|Title   	                 	
TestAd
Description  	
This is an test ad.
 
click_url  
 
URL of transition destination when click the ads http://click.mtburn.com/…
 
icon_image_url	      	
 
URL of icon style of quadrate picture            http://banner.dspcdn.com/…
 
main_image_url          	
 
URL of banner style rectangular picture   	http://banner.dspcdn.com/…
 
ad_id            	
ID of ads contentvertisement matter      	  	123
 
 
For Using DFP(DoubleClick for Publisher)DEP linkage
 
 
It is possible to deliver throughusing DFP. Insert tags you used, because how to implemented tags are different; whether tags are synchronized or not.
 
In case of nonsynchronous tags
 
 Add creatives to the order you want, and set the type of creatives pick out “third party”.
 
 Insert M.T.Burn JavaScript Tag as Creative.
 
Insert JavaScriptcalling tags, such as adsvertisement unit template and, empty element that assigns display placementlocation of adsvertisement, script for adsvertisement as ordinary.
 
In addition, it needs to insert CSSstyle sheet for display.
Insert click macro of DFP. Insert it accurately, and then it is possible to measuregrasp screen transitions and the number of clicks.
 
<!--  About reading CSScss. It needs to customize. -->
<link rel="stylesheet" type="text/css" href="http://yourodmain/sampale.css">
 
<!-- Template od adsvertisement unit -->
 <script type="text/advs-instream-template" data-adspot-id="NzYzOjEyMg">
<div class="article">
 <div class="icon">
 
// Insert click macro of DFP, and then write down click URL of M.T.Burn just beneath of click macro of DFP.
 
  <a href="%%CLICK_URL_UNESC%%{{click_url}}" target="_blank">
 	<img src="{{icon_image_url}}" />
   </a>
 </div>
 
 <div class="contents">
   <h3>{{title}}</h3>
   <p>{{description}}</p>
   <span class="source">Sponsored</span>
 </div>
</div>
</script>
 
<!-- Assigning display location of ads -->
<div data-advs-adspot-id="NzYzOjEyMg" style="display:none"></div>
 
 
<!-- Calling for adsvertisement script. -->　
<script src="https://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run({
   before_render:function(ad_info, placement_info) {
 
// You mustshould implement this step in order to connect DFP macro.
 
   	ad_info.click_url = encodeURIComponent(ad_info.click_url);
 
// If you want, it needs to implement like wrap up description.
// It is better to follow the procedure; otherwise the text’s jumbled.
 
   	var desc_max_len = 30;
   	if (ad_info.description.length > desc_max_len) {
       	ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
   	}
 
   	return ad_info;
   }
});
</script>
 
It needs to read a CSSstyle sheet additionally because of advertisement displaying ads in iframe.
 
In case of synchronized tags
 
 
 
 
 
 
Add creatives to the order you want, and set the type of creativespick out “Tthird party”.
Insert JavaScriptcalling tags such as advertisement unit template, empty element that assigns display placementlocation of advertisement, script for advertisement as ordinary.
Insert click macro of DFP. Insert it accurately, and then it is possible to measuregrasp screen transitions and the number of clicks.
 
<!--Template of adsvertisement units. -->
 <script type="text/advs-instream-template" data-adspot-id="NzYzOjEyMg">
<div class="article">
 <div class="icon">
<!-- Insert click macro of DFP, then put click URL of “M.T.Burn” right after-->
   <a href="%%CLICK_URL_UNESC%%{{click_url}}" target="_blank">
 	<img src="{{icon_image_url}}" />
   </a>
 </div>
 
 <div class="contents">
   <h3>{{title}}</h3>
   <p>{{description}}</p>
   <span class="source">Sponsored</span>
 </div>
</div>
</script>
 
 
<!--Assigning display placementlocation of ads. -->
<div data-advs-adspot-id="NzYzOjEyMg" style="display:none"></div>
 
 
<!-- Calling for adsvertisement script -->
 <script src="https://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run({
   before_render:function(ad_info, placement_info) {
   	
 
 
// You mustshould implement this step in order to connect DFP macro.
 
   	ad_info.click_url = encodeURIComponent(ad_info.click_url);
 
 
 
// Depending on media, it needs to implement like wrap up description.
// It is better to follow the procedure; otherwise the text’s jumbled.
 
  	var desc_max_len = 30;
   	if (ad_info.description.length > desc_max_len) {
       	ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
   	}
 
   	return ad_info;
   }
});
</script>
 
If you use synchronized tags, it doesn’t need to call for style sheets.
 
name space
 
 
It creates “MTBADVS” argumentfunction just beneath “window” object. Use “MTBADVS” argumentfunction, and then global namespace pollution never happens.
 
Q&A
 
What is meaning of “Instream ”in codes?
 
 
 It has same role as “In-Feed” in the guide.
 
 

