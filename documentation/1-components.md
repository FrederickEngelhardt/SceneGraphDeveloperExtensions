# SGDEX Components:  
* [BaseScene](#basescene)  
* [ContentHandler](#contenthandler)  
* [RAFHandler](#rafhandler)  
* [EntitlementHandler](#entitlementhandler)  
* [ComponentController](#componentcontroller)  
* [MediaView](#mediaview)  
* [SearchView](#searchview)  
* [TimeGridView](#timegridview)  
* [EntitlementView](#entitlementview)  
* [ParagraphView](#paragraphview)  
* [DetailsView](#detailsview)  
* [GridView](#gridview)  
* [VideoView](#videoview)  
* [CategoryListView](#categorylistview)  
* [SGDEXComponent](#sgdexcomponent)

____

## <a id="basescene"></a>BaseScene
### <a id="basescene#extends"></a>Extends: Scene
### <a id="basescene#description"></a>Description
developer should extend BaseScene and work in it's context.  
Function show(args) should be overrided in channel.

### <a id="basescene#interface"></a>Interface
#### <a id="basescene#fields"></a>Fields
  
* <a id="basescene#fields#componentcontroller"></a>**ComponentController** (node)
    * reference to ComponentController node that is created and used inside library  
    * Read Only  
* <a id="basescene#fields#exitchannel"></a>**exitChannel** (bool)
    * Exits channel if set to true  
    * Write Only  
* <a id="basescene#fields#theme"></a>**theme** (assocarray)
    * Theme is used to customize the appearance of all SGDEX views.  
For common fields see [SGDEXComponent](#sgdexcomponent)  
For view specific views see view documentation  
[GridView](#gridview)  
[DetailsView](#detailsview)  
[VideoView](#videoview)  
[CategoryListView](#categorylistview)  
<b>Theme can be set to several levels</b>  
<b>Any view:</b>  
<code>  
scene.theme = {  
&nbsp;&nbsp;global: {  
&nbsp;&nbsp;&nbsp;&nbsp;textColor: "FF0000FF"  
&nbsp;&nbsp;}  
}  
</code>  
Set's all text colors to red  
<b>Type of view</b>  
<code>  
scene.theme = {  
&nbsp;&nbsp;gridView: {  
&nbsp;&nbsp;&nbsp;&nbsp;textColor: "FF0000FF"  
&nbsp;&nbsp;}  
}  
</code>  
Set's all grids text color to red  
<b>Instance specific:</b>  
use view's theme field to set it's theme  
<code>  
view = CreateObject("roSGNode", "GridView")  
view.theme = {  
&nbsp;&nbsp;textColor: "FF0000FF"  
}  
</code>  
this grid will only have text color red  
All theme fields are combined and used by view when created, so you can set  
<code>  
scene.theme = {  
&nbsp;&nbsp;   global: {  
&nbsp;&nbsp;&nbsp;&nbsp;textColor: "FF0000FF"  
&nbsp;&nbsp;}  
&nbsp;&nbsp;gridView: {  
&nbsp;&nbsp;&nbsp;&nbsp;textColor: "00FF00FF"  
&nbsp;&nbsp;}  
}  
view1 = CreateObject("roSGNode", "GridView")  
view2 = CreateObject("roSGNode", "GridView")  
view.2theme = {  
&nbsp;&nbsp;textColor: "FFFFFFFF"  
}  
detailsView= CreateObject("roSGNode", "DetailsView")  
</code>  
In this case  
view1 - will have texts in 00FF00FF  
view2 - will have FFFFFFFF  
detailsView - will take textColor from global and it will be FF0000FF  
  
* <a id="basescene#fields#updatetheme"></a>**updateTheme** (assocarray)
    * Field to update themes by passing the config  
Structure of config is same as for [theme](#basescene) field.  
You should only pass fields that should be updated not all theme fields.  
Note. if you want to change a lot of fields change them with as smaller amount of configs as you can, it wouldn't redraw views too often then.  


### <a id="basescene#sample"></a>Sample of usage:
    // MainScene.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <component name="MainScene" extends="BaseScene" >
        <script type="text/brightscript" uri="pkg:/components/MainScene.brs" />
    </component>

    // MainScene.brs
    sub Show(args)
        homeGrid = CreateObject("roSGNode", "GridView")
        homeGrid.content = GetContentNodeForHome() ' implemented by developer
        homeGrid.ObserveField("rowItemSelected","OnGridItemSelected")
        'this will trigger job to show this View
        m.top.ComponentController.callFunc("show", {
            view: homeGrid
        })
    end sub



___

## <a id="contenthandler"></a>ContentHandler
### <a id="contenthandler#extends"></a>Extends: Task
### <a id="contenthandler#description"></a>Description
Content Handlers are responsible for all content loading tasks in SGDEX.  
When you extend a Content Handler, you must implement a function called GetContent().  
This function is where you will do things like make API requests and build ContentNodes  
to be rendered in your SGDEX views.

### <a id="contenthandler#interface"></a>Interface
#### <a id="contenthandler#fields"></a>Fields
  
* <a id="contenthandler#fields#content"></a>**content** (node)
    * This is the field you should modify in your GetContent() function  
by adding/updating the ContentNodes being rendered by the associated view.  
  
* <a id="contenthandler#fields#offset"></a>**offset** (int)
    * When working with paged data, this will reflect which page of content  
SGDEX is expecting the ContentHandler to populate.  
  
* <a id="contenthandler#fields#pagesize"></a>**pageSize** (int)
    * When working with paged data, this will reflect the number of items  
SGDEX is expecting the ContentHandler to populate.  
  
* <a id="contenthandler#fields#query"></a>**query** (string)
    * When working with SearchView, this will contain search query passed in config.  
  
* <a id="contenthandler#fields#failed"></a>**failed** (bool)
    * Default value: false
    * When your ContentHandler fails to load the requested content  
you should set this field to TRUE in your GetContent() function. This will  
force SGDEX to re-try the ContentHandler.  
In this case, you can also optionally set a new HandlerConfig to the content field.  
That will cause SGDEX to use the new config when it re-tries the ContentHandler.  
If you do not update the HandlerConfig, SGDEX will re-use the original one for subsequent tries.  
  
* <a id="contenthandler#fields#handlerconfig"></a>**HandlerConfig** (assocarray)
    * This is a copy of the config that was used to invoke the ContentHandler.  


### <a id="contenthandler#sample"></a>Sample of usage:
    ' SimpleContentHandler.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <component name="SimpleContentHandler" extends="ContentHandler" >
      <script type="text/brightscript" uri="pkg:/components/content/SimpleContentHandler.brs" />
    </component>

    ' SimpleContentHandler.brs
    sub GetContent()
      m.top.content.SetFields({
        title: "Hello World"
      })
    end sub


___

## <a id="rafhandler"></a>RAFHandler
### <a id="rafhandler#extends"></a>Extends: Task
### <a id="rafhandler#description"></a>Description
RAFHandler is responsible for making all business logic related to Ads playing.  
developer extends this Handler in channel and can override ConfigureRAF(adIface as Object) sub.  
Reference to Raf library instance will be passed to ConfigureRAF sub.  
In ConfigureRAF developer can make any configuraion that supported by RAF.



### <a id="rafhandler#sample"></a>Sample of usage:
    sub ConfigureRAF(adIface)
        ' Detailed RAF docs: https://sdkdocs.roku.com/display/sdkdoc/Integrating+the+Roku+Advertising+Framework
        adIface.SetAdUrl("http://www.some.ad.url.com")
        adIface.SetContentGenre("General Variety")
        adIface.SetContentLength(1200) ' in seconds
        ' Nielsen specific data
        adIface.EnableNielsenDAR(true)
        adIface.SetNielsenProgramId("CBAA")
        adIface.SetNielsenGenre("GV")
        adIface.SetNielsenAppId("P123QWE-1A2B-1234-5678-C7D654348321")
    end sub


___

## <a id="entitlementhandler"></a>EntitlementHandler
### <a id="entitlementhandler#extends"></a>Extends: Task
### <a id="entitlementhandler#description"></a>Description
developer should implement own Handler in channel that extends EntitlementHandler  
In this handler developer can override 2 function:  
- ConfigureEntitlements(config) [Required]  
- OnPurchaseSuccess(transactionData) [Optional]  
In ConfigureEntitlements developer can update config with his own params:  
config should contain fields:  
config.mode [String] the desired mode. If mode isn't specified then it defaults to "RokuBilling" for backward compatibility.   
Supported values are:  
"RokuBilling" – SVOD Roku Billing;  
"UserPass" – username/password authentication.  
When config.mode = "RokuBilling" or not specified:  
config.products [Array] product configuration as an array of AAs:   
config.products[i].code [String] product code in ChannelStore  
config.products[i].hasTrial [Boolean] true if product has trial period  
OnPurchaseSuccess is a callback that allows end developer to inject some suctom logic on purchase success  
by overriding this subroutine. Default implementation does nothing.

### <a id="entitlementhandler#interface"></a>Interface
#### <a id="entitlementhandler#fields"></a>Fields
  
* <a id="entitlementhandler#fields#content"></a>**content** (node)
    * Content node from EntitlementView will be passed here, developer can use it in handler.  
  
* <a id="entitlementhandler#fields#view"></a>**view** (node)
    * View is a reference to EntitlementView where this Handler is created.  


### <a id="entitlementhandler#sample"></a>Sample of usage:
    // [In <component name="HandlerEntitlement" extends="EntitlementHandler"> in channel]
    sub ConfigureEntitlements(config as Object)
        config.products = [
            '{code: "PROD1", hasTrial: false}
            {code: "PROD2", hasTrial: false}
        ]
    end sub


___

## <a id="componentcontroller"></a>ComponentController
### <a id="componentcontroller#extends"></a>Extends: Group
### <a id="componentcontroller#description"></a>Description
ComponentController (CC) is a node that responsible to make basic View interaction logic.  
From developer side, CC is used to show Views for different use cases.  
There are 2 flags to handle close behaviour:  
allowCloseChannelOnLastView:bool=true and allowCloseLastViewOnBack:bool=true

### <a id="componentcontroller#interface"></a>Interface
#### <a id="componentcontroller#fields"></a>Fields
  
* <a id="componentcontroller#fields#currentview"></a>**currentView** (node)
    * holds the reference to view that is currently shown.  
Can be used for checking in onkeyEvent  
  
* <a id="componentcontroller#fields#allowclosechannelonlastview"></a>**allowCloseChannelOnLastView** (boolean)
    * Default value: true
    * If developer set this flag channel closes when press back or set close=true on last view  
  
* <a id="componentcontroller#fields#allowcloselastviewonback"></a>**allowCloseLastViewOnBack** (boolean)
    * Default value: true
    * If developer set this flag the last View will be closed and developer can open another in wasClosed callback  

#### <a id="componentcontroller#functions"></a>Functions
* <a id="componentcontroller#functions#show"></a>**show**
    * Function that has to be called when you want to add view to view stack

### <a id="componentcontroller#sample"></a>Sample of usage:
    ' in Scene context in channel
    m.top.ComponentController.callFunc("show", {
        view: View
    })
 

___

## <a id="mediaview"></a>MediaView
### <a id="mediaview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="mediaview#description"></a>Description
Media View is a component that provide pre-defined approach to play video or audio  
It incapsulates different features:  
- Playback of video or audio  
- Playback of playlist or one item;  
- Two different UI modes for audio and video items;  
- Ability to set mixed content (both video and audio);  
- Loading of content via HandlerConfigMedia before playback;  
- Showing Endcards View after playback ends;  
- Loading of endcard content via HandlerConfigEndcard some time before playback ends to provide smooth user experience;  
- Handling of RAF - handlerConfigRAF should be set in content node;  
- State field is aliased to make tracking of states easier;  
- Themes support

### <a id="mediaview#interface"></a>Interface
#### <a id="mediaview#fields"></a>Fields
  
* <a id="mediaview#fields#mode"></a>**mode** (string)
    * Default value: video
    * Defines view mode to properly display media content on the view  
  
* <a id="mediaview#fields#endcardcountdowntime"></a>**endcardCountdownTime** (integer)
    * Default value: 10
    * Endcard countdown time. How much endcard is shown until next video start  
  
* <a id="mediaview#fields#endcardloadtime"></a>**endcardLoadTime** (integer)
    * Default value: 10
    * Time to end when endcard content start load  
  
* <a id="mediaview#fields#alwaysshowendcards"></a>**alwaysShowEndcards** (bool)
    * Default value: false
    * Config to know should Media View show endcards with default next item and Repeat button  
even if there is no content getter specified by developer  
  
* <a id="mediaview#fields#iscontentlist"></a>**isContentList** (bool)
    * Default value: true
    * Sets the operating mode of the view.  
 When true, the playlist of content is represented by the children of the root ContentNode.  
 When false, the root ContentNode itself is treated as a single piece of content.  
 This field must be set before setting the content field.  
  
* <a id="mediaview#fields#jumptoitem"></a>**jumpToItem** (integer)
    * Default value: 0
    * Jumps to item in playlist  
  This field must be set after setting the content field.  
  
* <a id="mediaview#fields#control"></a>**control** (string)
    * Default value: none
    * Control "play" and "prebuffer" makes library start to load content from Content Getter  
if any other control - set it directly to video node  
  
* <a id="mediaview#fields#preloadcontent"></a>**preloadContent** (boolean)
    * Default value: false
    * Trigger to notify that next item in playlist should be preloaded while Endcard view is shown  
  
* <a id="mediaview#fields#endcardtrigger"></a>**endcardTrigger** (boolean)
    * Default value: false
    * Trigger to notify channel that endcard loading is started  
  
* <a id="mediaview#fields#currentindex"></a>**currentIndex** (integer)
    * Default value: -1
    * Field to know what is index of current item - index of child in content Content Node  
  
* <a id="mediaview#fields#state"></a>**state** (string)
    * Default value: none
    * Media Node state  
  
* <a id="mediaview#fields#position"></a>**position** (int)
    * Default value: 0
    * Playback position in seconds  
  
* <a id="mediaview#fields#duration"></a>**duration** (int)
    * Default value: 0
    * Playback duration in seconds  
  
* <a id="mediaview#fields#currentitem"></a>**currentItem** (node)
    * If change this field manually, unexpected behaviour can occur.  
    * Read Only  
* <a id="mediaview#fields#endcarditemselected"></a>**endcardItemSelected** (node)
    * Content node of endcard item what was selected  
  
* <a id="mediaview#fields#disablescreensaver"></a>**disableScreenSaver** (boolean)
    * Default value: false
    * This is an alias of the video node's field of the same name  
  https://developer.roku.com/docs/references/scenegraph/media-playback-nodes/video.md#miscellaneous-fields  
  This field only works in video mode  
  
* <a id="mediaview#fields#theme"></a>**theme** (assocarray)
    * Theme is used to change color of grid view elements  
<b>Note.</b> you can set TextColor and focusRingColor to have generic theme and only change attributes that shouldn't use it.  
<b>Possible fields:</b>  
<b>General fields</b>  
	* Possible values  
     * TextColor - set text color for all texts on video and endcard views
     * progressBarColor - set color for progress bar
     * focusRingColor - set color for focus ring on endcard view
     * backgroundColor - set background color for endcards view
     * backgroundImageURI - set background image url for endcards view
     * endcardGridBackgroundColor -set background color for grid for endcard items  
<b>Video player fields:</b>
     * trickPlayBarTextColor - Sets the color of the text next to the trickPlayBar node indicating the time elapsed/remaining.
     * trickPlayBarTrackImageUri - A 9-patch or ordinary PNG of the track of the progress bar, which surrounds the filled and empty bars. This will be blended with the color specified by the trackBlendColor field, if set to a non-default value.
     * trickPlayBarTrackBlendColor - This color is blended with the graphical image specified by trackImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarThumbBlendColor - Sets the blend color of the square image in the trickPlayBar node that shows the current position, with the current direction arrows or pause icon on top. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarFilledBarImageUri - A 9-patch or ordinary PNG of the bar that represents the completed portion of the work represented by this ProgressBar node. This is typically displayed on the left side of the track. This will be blended with the color specified by the filledBarBlendColor field, if set to a non-default value.
     * trickPlayBarFilledBarBlendColor - This color will be blended with the graphical image specified in the filledBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarCurrentTimeMarkerBlendColor - This is blended with the marker for the current playback position. This is typically a small vertical bar displayed in the TrickPlayBar node when the developer is fast-forwarding or rewinding through the video.  
<b>Buffering Bar customization</b>
     * bufferingTextColor - The color of the text displayed near the buffering bar defined by the bufferingBar field, when the buffering bar is visible. If this is 0, the system default color is used. To set a custom color, set this field to a value other than 0x0.
     * bufferingBarEmptyBarImageUri - A 9-patch or ordinary PNG of the bar presenting the remaining work to be done. This is typically displayed on the right side of the track, and is blended with the color specified in the emptyBarBlendColor field, if set to a non-default value.
     * bufferingBarFilledBarImageUri - A 9-patch or ordinary PNG of the bar that represents the completed portion of the work represented by this ProgressBar node. This is typically displayed on the left side of the track. This will be blended with the color specified by the filledBarBlendColor field, if set to a non-default value.
     * bufferingBarTrackImageUri - A 9-patch or ordinary PNG of the track of the progress bar, which surrounds the filled and empty bars. This will be blended with the color specified by the trackBlendColor field, if set to a non-default value.
     * bufferingBarTrackBlendColor - This color is blended with the graphical image specified by trackImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * bufferingBarEmptyBarBlendColor - A color to be blended with the graphical image specified in the emptyBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * bufferingBarFilledBarBlendColor - This color will be blended with the graphical image specified in the filledBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.  
<b>Retrieving Bar customization</b>
     * retrievingTextColor - Same as bufferingTextColor but for retrieving bar
     * retrievingBarEmptyBarImageUri - Same as bufferingBarEmptyBarImageUri but for retrieving bar
     * retrievingBarFilledBarImageUri - Same as bufferingBarFilledBarImageUri but for retrieving bar
     * retrievingBarTrackImageUri - Same as bufferingBarTrackImageUri but for retrieving bar
     * retrievingBarTrackBlendColor - Same as bufferingBarTrackBlendColor but for retrieving bar
     * retrievingBarEmptyBarBlendColor - Same as bufferingBarEmptyBarBlendColor but for retrieving bar
     * retrievingBarFilledBarBlendColor - Same as bufferingBarFilledBarBlendColor but for retrieving bar  
<b>BIF customization</b>
     * focusRingColor - a color to be blended with the image displayed behind individual BIF images displayed on the screen  
<b>Endcard view theme attributes</b>
     * buttonsFocusedColor - repeat button focused text color
     * buttonsUnFocusedColor - repeat button unfocused text color
     * buttonsfocusRingColor - repeat button background color  
<b>grid attributes</b>
     * rowLabelColor - grid row title color
     * focusRingColor - grid focus ring color
     * focusFootprintBlendColor - grid unfocused focus ring color
     * itemTextColorLine1 - text color for 1st row on endcard item
     * itemTextColorLine2 - text color for 2nd row on endcard item
     * timerLabelColor - Color of remaining timer


___

## <a id="searchview"></a>SearchView
### <a id="searchview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="searchview#description"></a>Description
SearchView requires firmware v9.1 or newer

### <a id="searchview#interface"></a>Interface
#### <a id="searchview#fields"></a>Fields
  
* <a id="searchview#fields#rowitemfocused"></a>**rowItemFocused** (vector2d)
    * Read only  
Updated when grid focused item changes  
Value is an array containing the index of the row and item that were focused  
  
* <a id="searchview#fields#rowitemselected"></a>**rowItemSelected** (vector2d)
    * Read only  
Updated when an item on the grid with result is selected  
Value is an array containing the index of the row and item that were selected  
  
* <a id="searchview#fields#shownoresultslabel"></a>**showNoResultsLabel** (boolean)
    * Default value: true
    * If set to true and there is no search results then label with text  
specified in noResultsLabelText field will be shown instead of the grid  
  
* <a id="searchview#fields#noresultslabeltext"></a>**noResultsLabelText** (string)
    * Default value: No results
    * Specifies the text which will be shown in case if there is  
no search results and showNoResultsLabel is true  
  
* <a id="searchview#fields#hinttext"></a>**hintText** (string)
    * Specifies a string to be displayed if the length of the input text is zero  
The typical usage of this field is to prompt the user about what to enter  
  
* <a id="searchview#fields#query"></a>**query** (string)
    * Read only  
The text entered by the user  
  
* <a id="searchview#fields#theme"></a>**theme** (assocarray)
    * Controls the color of visual elements  
	* Possible values  
     * textColor - sets the color of all text elements in the view
     * focusRingColor - sets the color of focus ring
     * keyboardKeyColor - sets the color of the key labels and icons when the Keyboard node does not have the focus
     * keyboardFocusedKeyColor - sets the color of the key labels and icons when the Keyboard node has the focus
     * textBoxTextColor - sets the color of the text string displayed in the TextBox
     * textBoxHintColor - sets the color of the hint text string
     * noResultsLabelColor - sets the color of the label which is shown when there is no results
     * rowLabelColor - sets the color for row titles
     * focusFootprintColor - sets color for focus ring when unfocused
     * itemTextColorLine1 - sets color for first row in short description
     * itemTextColorLine2 - sets color for second row in short description
  
* <a id="searchview#fields#updatetheme"></a>**updateTheme** (assocarray)
    * updateTheme is used to update view specific theme fields  
Usage is same as [theme](#sgdexcomponent) field but here you should only set field that you want to update  
If you want global updates use [BaseScene updateTheme](#basescene)  
  
* <a id="searchview#fields#postershape"></a>**posterShape** (string)
    * Controls the aspect ratio of the posters on the result grid  
	* Possible values  
     * 16x9
     * portrait
     * 4x3
     * square
  
* <a id="searchview#fields#content"></a>**content** (node)
    * Controls how SGDEX will load the content for search result  


### <a id="searchview#sample"></a>Sample of usage:
    Content metadata field that can be used:
    root = {
        children:[{
            title: "Row title"
            children: [{
                title: "title that will be shown on upper details section"
                description: "Description that will be shown on upper details section"
                hdPosterUrl: "Poster URL that should be shown on grid item"

                releaseDate: "Release date string"
                StarRating: "star rating integer between 0 and 100"

                gridItemMetaData: "fields that are used on grid"

                shortDescriptionLine1: "first row that will be displayed on grid"
                shortDescriptionLine2: "second row that will be displayed on grid"

                _forShowing_bookmarks_on_grid: "Add these fields to show bookmarks on grid item"
                length: "length of video"
                bookmarkposition: "actual bookmark position"

                _note: "tels if this bar should be hidden, default = true"
                hideItemDurationBar: false
            }]
        }]
    }

    ' If you have to make API call to get search result you should observe query field and
    ' perform loading content like this:

    searchView = CreateObject("roSGNode", "SearchView")
    searchView.ObserveFieldScoped("query", "OnSearchQuery")

    sub OnSearchQuery(event as Object)
        query = event.GetData()
        content = CreateObject("roSGNode", "ContentNode")
        if query.Len() > 2 ' only search if user has typed at least three characters
            content.AddFields({
                HandlerConfigSearch: {
                    name: "CHRoot"
                    query: query
                }
            })
        end if
        ' setting the content with handlerConfigSearch will trigger creation
        ' of grid view and its content manager
        ' setting an empty content node clears the grid
        event.GetRoSGNode().content = content
    end sub

    ' Where CHRoot is a ContentHandler that is responsible for getting search results for grid


___

## <a id="timegridview"></a>TimeGridView
### <a id="timegridview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="timegridview#description"></a>Description
TimeGridView represents SGDEX view that is responsible for:  
- Rendering TimeGrid node  
- content loading with different content models  
- loading channels content when user is navigating  
- lazy loading of channels in IDLE

### <a id="timegridview#interface"></a>Interface
#### <a id="timegridview#fields"></a>Fields
  
* <a id="timegridview#fields#contentstarttime"></a>**contentStartTime** (integer)
    * Alias to the TimeGrid contentStartTime field  
The earliest time that the TimeGrid can be moved to  
  
* <a id="timegridview#fields#maxdays"></a>**maxDays** (integer)
    * Alias to the TimeGrid maxDays field  
Specifies the total width of the TimeGrid in days  
  
* <a id="timegridview#fields#rowitemselected"></a>**rowItemSelected** (array)
    * Updated when user selects a program from the TimeGrid  
Value is an array of indexes represents [channelIndex, programIndex]  
updated simultaneously with channelSelected and programSelected  


### <a id="timegridview#sample"></a>Sample of usage:
    timeGrid = CreateObject("roSGNode", "TimeGridView")
    content = CreateObject("roSGNode", "ContentNode")
    content.addfields({
        HandlerConfigTimeGrid: {
            name: "HCTimeGrid"
        }
    })
    timeGrid.content = content
    timeGrid.observeField("rowItemSelected", "OnTimeGridRowItemSelected")

    m.top.ComponentController.callFunc("show", {
        view: timeGrid
    })


___

## <a id="entitlementview"></a>EntitlementView
### <a id="entitlementview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="entitlementview#description"></a>Description
EntitlementView is a view that allows SGDEX developer to make subscription easy.  
There are two basic behaviours:  
1) Silen check of available subscription  
2) Checking with show Entitlement view/flow  
To pass configs to View, developer should implement handler that extends EntitlementHandler

### <a id="entitlementview#interface"></a>Interface
#### <a id="entitlementview#fields"></a>Fields
  
* <a id="entitlementview#fields#issubscribed"></a>**isSubscribed** (bool)
    * Default value: false
    * [ObserveOnly] sets to true|false and shows if developer is subscribed after:  
1) checking via silentCheckEntitlement=true (see below)  
2) exitting from subscription flow initiated by adding view to View stack  
  
* <a id="entitlementview#fields#silentcheckentitlement"></a>**silentCheckEntitlement** (bool)
    * Default value: false
    * initiates silent entitlement checking (no UI)  
    * Write Only  
* <a id="entitlementview#fields#isauthenticated"></a>**isAuthenticated** (bool)
    * Default value: false
    * [ObserveOnly] sets to true|false and shows if developer is authenticated after:  
1) checking via silentCheckAuthentication=true (see below)  
2) exitting from authentication flow initiated by adding view to View stack  
  
* <a id="entitlementview#fields#prepopulateemail"></a>**prepopulateEmail** (bool)
    * Default value: false
    * a boolean telling SGDEX whether it should prompt the user to share their  
Roku account email address and use that value to pre-populate the KeyboardView.  
    * Write Only  
* <a id="entitlementview#fields#silentcheckauthentication"></a>**silentCheckAuthentication** (bool)
    * Default value: false
    * initiates silent authentication checking (no UI)  
    * Write Only  
* <a id="entitlementview#fields#silentdeauthenticate"></a>**silentDeAuthenticate** (bool)
    * Default value: false
    * initiates silent de-authentication (no UI)  
    * Write Only  
* <a id="entitlementview#fields#username"></a>**username** (string)
    * a string field which contains username. This field used in case of   
custom UI for collecting the credentials.  
  
* <a id="entitlementview#fields#password"></a>**password** (string)
    * a string field which contains user password. This field used in case of   
custom UI for collecting the credentials.  


### <a id="entitlementview#sample"></a>Sample of usage:
    // [In channel]
    // contentItem - content node with handlerConfigEntitlement: {name : "HandlerEntitlement"}

    // To make just silent check if developer subscribed
    ent = CreateObject("roSGNode", "EntitlementView")
    ent.ObserveField("isSubscribed", "OnSubscriptionChecked")
    ent.content = contentItem
    ent.silentCheckEntitlement = true

    // To show billing flow:
    ent = CreateObject("roSGNode","EntitlementView")
    ent.ObserveField("isSubscribed", "OnIsSubscribedToPlay")
    ent.content = contentItem
    m.top.ComponentController.callFunc("show", {view: ent})
 

___

## <a id="paragraphview"></a>ParagraphView
### <a id="paragraphview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="paragraphview#description"></a>Description


### <a id="paragraphview#interface"></a>Interface
#### <a id="paragraphview#fields"></a>Fields
  
* <a id="paragraphview#fields#buttons"></a>**buttons** (node)
    * Content node for buttons node. Has childrens with id and title that will be shown on View.  
  
* <a id="paragraphview#fields#buttonfocused"></a>**buttonFocused** (integer)
    * Tells what button is focused  
    * Read Only  
* <a id="paragraphview#fields#buttonselected"></a>**buttonSelected** (integer)
    * Is set when button is selected by user. Should be observed in channel.  
Can be used for showing next View or start playback or so.  
    * Read Only  
* <a id="paragraphview#fields#jumptobutton"></a>**jumpToButton** (integer)
    * Interface for setting focused button  
    * Write Only  
* <a id="paragraphview#fields#theme"></a>**theme** (assocarray)
    * Controls the color of visual elements  
	* Possible values  
     * textColor - sets the color of all text elements in the view
     * paragraphColor - specify the color of text with type paragraph
     * headerColor - specify the color of text with type header
     * linkingCodeColor - specify the color of text with type linkingCode
     * buttonsFocusedColor - set the color of focused buttons
     * buttonsUnFocusedColor - set the color of unfucused buttons
     * buttonsFocusRingColor - set the color of button focuse ring
  
* <a id="paragraphview#fields#updatetheme"></a>**updateTheme** (assocarray)
    * updateTheme is used to update view specific theme fields  
Usage is same as [theme](#sgdexcomponent) field but here you should only set field that you want to update  
If you want global updates use [BaseScene updateTheme](#basescene)  
  
* <a id="paragraphview#fields#content"></a>**content** (node)
    * Contains content to display on the ParagraphView or HandlerConfigParagraph  


___

## <a id="detailsview"></a>DetailsView
### <a id="detailsview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="detailsview#description"></a>Description
buttons support same content meta-data fields as Label list, so you can set title and small icon for each button  
fields description:  
TITLE - string  The label for the list item  
HDLISTITEMICONURL - uri The image file for the icon to be displayed to the left of the list item label when the list item is not focused  
HDLISTITEMICONSELECTEDURL - uri The image file for the icon to be displayed to the left of the list item label when the list item is focused

### <a id="detailsview#interface"></a>Interface
#### <a id="detailsview#fields"></a>Fields
  
* <a id="detailsview#fields#buttons"></a>**buttons** (node)
    * Content node for buttons node. Has childrens with id and title that will be shown on View.  
  
* <a id="detailsview#fields#iscontentlist"></a>**isContentList** (bool)
    * Default value: true
    * Tells details view how your content is structured  
if set to true it will take children of _content_ to display on View  
if set to false it will take _content_ and display it on the View  
    * Write Only  
* <a id="detailsview#fields#allowwrapcontent"></a>**allowWrapContent** (bool)
    * Default value: true
    * defines logic of showing content when pressing left on first item, or pressing right on last item.  
if set to true it will start from start from first item (when pressing right) or last item (when pressing left)  
    * Write Only  
* <a id="detailsview#fields#currentitem"></a>**currentItem** (node)
    * Current displayed item. This item is set when Content Getter finished loading extra meta-data  
    * Read Only  
* <a id="detailsview#fields#itemfocused"></a>**itemFocused** (integer)
    * tells what item is currently focused  
  
* <a id="detailsview#fields#itemloaded"></a>**itemLoaded** (bool)
    * itemLoaded is set to true when currentItem field is populated with new content node when content available or loaded  
  
* <a id="detailsview#fields#jumptoitem"></a>**jumpToItem** (integer)
    * Default value: 0
    * Manually focus on desired item. This field must be set after setting the content field.  
    * Write Only  
* <a id="detailsview#fields#buttonfocused"></a>**buttonFocused** (integer)
    * Tells what button is focused  
    * Read Only  
* <a id="detailsview#fields#buttonselected"></a>**buttonSelected** (integer)
    * Is set when button is selected by user. Should be observed in channel.  
Can be used for showing next View or start playback or so.  
    * Read Only  
* <a id="detailsview#fields#jumptobutton"></a>**jumpToButton** (integer)
    * Interface for setting focused button  
    * Write Only  
* <a id="detailsview#fields#theme"></a>**theme** (assocarray)
    * Controls the color of visual elements  
	* Possible values  
     * textColor - sets the color of all text elements in the view
     * focusRingColor - set color of focus ring
     * focusFootprintColor - set color for focus ring when unfocused
     * rowLabelColor - sets color for row title
     * descriptionColor -set the color of descriptionLabel
     * actorsColor -set the color of actorsLabel
     * ReleaseDateColor -set the the color for ReleaseDate
     * RatingAndCategoriesColor -set the color of categories
     * buttonsFocusedColor - set the color of focused buttons
     * buttonsUnFocusedColor - set the color of unfucused buttons
     * buttonsFocusRingColor - set the color of button focuse ring
     * buttonsSectionDividerTextColor - set the color of section divider


___

## <a id="gridview"></a>GridView
### <a id="gridview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="gridview#description"></a>Description
Grid view represents SGDEX grid that is responsible for:  
- content loading  
- lazy loading of rows and item in row  
- loading pages of content  
- lazy loading rows when user is not navigating

### <a id="gridview#interface"></a>Interface
#### <a id="gridview#fields"></a>Fields
  
* <a id="gridview#fields#rowitemfocused"></a>**rowItemFocused** (vector2d)
    * Updated when focused item changes  
Value is an array containing the index of the row and item that were focused  
  
* <a id="gridview#fields#rowitemselected"></a>**rowItemSelected** (vector2d)
    * Updated when an item is selected  
Value is an array containing the index of the row and item that were selected  
  
* <a id="gridview#fields#jumptorowitem"></a>**jumpToRowItem** (vector2d)
    * Set grid focus to specified item  
Value is an array containing the index of the row and item that should be focused  
This field must be set after setting the content field.  
  
* <a id="gridview#fields#jumptorow"></a>**jumpToRow** (integer)
    * Set grid focus to specified row  
Value is an integer index of the row that should be focused  
This field must be set after setting the content field.  
  
* <a id="gridview#fields#rowpostershapes"></a>**rowPosterShapes** (stringarray)
    * Interface to support different poster shapes for grid rows.  
Value is an array of strings, which set row poster shapes.  
If the array contains fewer elements than the number of rows, then the shape of rest rows will be set to posterShape field 	or to the last value in the array.  
  
* <a id="gridview#fields#showmetadata"></a>**showMetadata** (boolean)
    * Default value: true
    * Controls whether the view displays metadata for the focused item above the grid  
  
* <a id="gridview#fields#theme"></a>**theme** (assocarray)
    * Controls the color of visual elements  
	* Possible values  
     * textColor - sets the color of all text elements in the view
     * focusRingColor - set color of focus ring
     * focusFootprintColor - set color for focus ring when unfocused
     * rowLabelColor - sets color for row title
     * itemTextColorLine1 - set color for first row in short description
     * itemTextColorLine2 - set color for second row in short description
     * itemTextBackgroundColor - set a background color for the short description
     * titleColor - sets color of title
     * descriptionColor - sets color of description text
     * descriptionmaxWidth - sets max width for description
     * descriptionMaxLines - sets max lines for description
  
* <a id="gridview#fields#style"></a>**style** (string)
    * Styles are used to tell what grid UI will be used  
	* Possible values  
     * standard - This is default grid style
     * hero - This style will display larger posters on the top row of the grid
     * zoom - This style will enable an animated zoom effect when a row gains focus
  
* <a id="gridview#fields#postershape"></a>**posterShape** (string)
    * Controls the aspect ratio of the posters on the grid  
	* Possible values  
     * 16x9
     * portrait
     * 4x3
     * square
  
* <a id="gridview#fields#content"></a>**content** (node)
    * Controls how SGDEX will load the content for the view  


### <a id="gridview#sample"></a>Sample of usage:
    grid = CreateObject("roSGNode", "GridView")
    grid.setFields({
        style: "standard"
        posterShape: "16x9"
    })
    content = CreateObject("roSGNode", "ContentNode")
    content.addfields({
        HandlerConfigGrid: {
            name: "CHRoot"
        }
    })
    grid.content = content

    'this will trigger job to show this View
    m.top.ComponentController.callFunc("show", {
        view: grid
    })



    ' Content metadata field that can be used:

    root = {

        children:[{
            title: "Row title"

            children: [{

                title: "title that will be shown on upper details section"
                description: "Description that will be shown on upper details section"
                hdPosterUrl: "Poster URL that should be shown on grid item"

                releaseDate: "Release date string"
                StarRating: "star rating integer between 0 and 100"

                _gridItemMetaData: "fields that are used on grid"

                shortDescriptionLine1: "first row that will be displayed on grid"
                shortDescriptionLine2: "second row that will be displayed on grid"


                _forShowing_bookmarks_on_grid: "Add these fields to show bookmarks on grid item"
                length: "length of video"
                bookmarkposition: "actual bookmark position"

                _note: "tels if this bar should be hidden, default = true"
                hideItemDurationBar: false

            }]
        }]
    }

    ' If you have to make API call to get list of rows set content like this:

        content = CreateObject("roSGNode", "ContentNode")
        content.addfields({
            HandlerConfigGrid: {
                name: "CHRoot"
            }
        })
        grid.content = content

    ' Where CHRoot is a ContentHandler that is responsible for getting rows for grid

    ' IF you know the structure of your grid but need to load content to rows you can do:

        content = CreateObject("roSGNode", "ContentNode")

        row = CreateObject("roSGNode", "ContentNode")
        row.title = "first row"
        row.addfields({
            HandlerConfigGrid: {
                name: "ContentHandlerForRows"
                fields : {
                    myField1 : "value I need to pass to content handler"
                }
            }
        })
        grid.content = content

    ' Where
    '    1) "ContentHandlerForRows" is content handler that will be called to get content for provided row.
    '    2) fields is AA of values that will be set to ContentHandler so you can pass additional data to ContentHandler
    '    Note. that passing row itself or grid via fields might cause memory leaks

    ' You can set row ContentHandler even when parsing content in "CHRoot", so it will be called when data for that row is needed


___

## <a id="videoview"></a>VideoView
### <a id="videoview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="videoview#description"></a>Description
Video View is a component that provide pre-defined approach to play Video  
It incapsulates different features:  
- Playback of video playlist or one item;  
- Loading of content via HandlerConfigVideo before playback;  
- Showing Endcards View after video playback ends;  
- Loading of endcard content via HandlerConfigEndcard some time before video ends to provide smooth user experience;  
- Handling of Raf - handlerConfigRAF should be set in content node;  
- Video.state field is aliased to make tracking of states easier;  
- Themes support

### <a id="videoview#interface"></a>Interface
#### <a id="videoview#fields"></a>Fields
  
* <a id="videoview#fields#endcardcountdowntime"></a>**endcardCountdownTime** (integer)
    * Default value: 10
    * Endcard countdown time. How much endcard is shown until next video start  
  
* <a id="videoview#fields#endcardloadtime"></a>**endcardLoadTime** (integer)
    * Default value: 10
    * Time to end when endcard content start load  
  
* <a id="videoview#fields#alwaysshowendcards"></a>**alwaysShowEndcards** (bool)
    * Default value: false
    * Config to know should Video View show endcards with default next item and Repeat button  
even if there is no content getter specified by developer  
  
* <a id="videoview#fields#iscontentlist"></a>**isContentList** (bool)
    * Default value: true
    * To make library know is it playlist or individual item  
  
* <a id="videoview#fields#jumptoitem"></a>**jumpToItem** (integer)
    * Jumps to item in playlist  
  This field must be set after setting the content field.  
  
* <a id="videoview#fields#control"></a>**control** (string)
    * Control "play" and "prebuffer" makes library start to load content from Content Getter  
if any other control - set it directly to video node  
  
* <a id="videoview#fields#endcardtrigger"></a>**endcardTrigger** (boolean)
    * Trigger to notify channel that endcard loading is started  
  
* <a id="videoview#fields#preloadcontent"></a>**preloadContent** (boolean)
    * Default value: false
    * Trigger to notify that next Video item in playlist should be preloaded while Endcard view is shown  
  
* <a id="videoview#fields#currentindex"></a>**currentIndex** (integer)
    * Default value: -1
    * Field to know what is index of current item - index of child in content Content Node  
  
* <a id="videoview#fields#state"></a>**state** (string)
    * Video Node state  
  
* <a id="videoview#fields#position"></a>**position** (int)
    * Playback position in seconds  
  
* <a id="videoview#fields#duration"></a>**duration** (int)
    * Playback duration in seconds  
  
* <a id="videoview#fields#currentitem"></a>**currentItem** (node)
    * If change this field manually, unexpected behaviour can occur.  
    * Read Only  
* <a id="videoview#fields#endcarditemselected"></a>**endcardItemSelected** (node)
    * Content node of endcard item what was selected  
  
* <a id="videoview#fields#disablescreensaver"></a>**disableScreenSaver** (boolean)
    * Default value: false
    * This is an alias of the video node's field of the same name  
  https://developer.roku.com/docs/references/scenegraph/media-playback-nodes/video.md#miscellaneous-fields  
  
* <a id="videoview#fields#theme"></a>**theme** (assocarray)
    * Theme is used to change color of grid view elements  
<b>Note.</b> you can set TextColor and focusRingColor to have generic theme and only change attributes that shouldn't use it.  
<b>Possible fields:</b>  
<b>General fields</b>  
	* Possible values  
     * TextColor - set text color for all texts on video and endcard views
     * progressBarColor - set color for progress bar
     * focusRingColor - set color for focus ring on endcard view
     * backgroundColor - set background color for endcards view
     * backgroundImageURI - set background image url for endcards view
     * endcardGridBackgroundColor -set background color for grid for endcard items  
<b>Video player fields:</b>
     * trickPlayBarTextColor - Sets the color of the text next to the trickPlayBar node indicating the time elapsed/remaining.
     * trickPlayBarTrackImageUri - A 9-patch or ordinary PNG of the track of the progress bar, which surrounds the filled and empty bars. This will be blended with the color specified by the trackBlendColor field, if set to a non-default value.
     * trickPlayBarTrackBlendColor - This color is blended with the graphical image specified by trackImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarThumbBlendColor - Sets the blend color of the square image in the trickPlayBar node that shows the current position, with the current direction arrows or pause icon on top. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarFilledBarImageUri - A 9-patch or ordinary PNG of the bar that represents the completed portion of the work represented by this ProgressBar node. This is typically displayed on the left side of the track. This will be blended with the color specified by the filledBarBlendColor field, if set to a non-default value.
     * trickPlayBarFilledBarBlendColor - This color will be blended with the graphical image specified in the filledBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * trickPlayBarCurrentTimeMarkerBlendColor - This is blended with the marker for the current playback position. This is typically a small vertical bar displayed in the TrickPlayBar node when the developer is fast-forwarding or rewinding through the video.  
<b>Buffering Bar customization</b>
     * bufferingTextColor - The color of the text displayed near the buffering bar defined by the bufferingBar field, when the buffering bar is visible. If this is 0, the system default color is used. To set a custom color, set this field to a value other than 0x0.
     * bufferingBarEmptyBarImageUri - A 9-patch or ordinary PNG of the bar presenting the remaining work to be done. This is typically displayed on the right side of the track, and is blended with the color specified in the emptyBarBlendColor field, if set to a non-default value.
     * bufferingBarFilledBarImageUri - A 9-patch or ordinary PNG of the bar that represents the completed portion of the work represented by this ProgressBar node. This is typically displayed on the left side of the track. This will be blended with the color specified by the filledBarBlendColor field, if set to a non-default value.
     * bufferingBarTrackImageUri - A 9-patch or ordinary PNG of the track of the progress bar, which surrounds the filled and empty bars. This will be blended with the color specified by the trackBlendColor field, if set to a non-default value.
     * bufferingBarTrackBlendColor - This color is blended with the graphical image specified by trackImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * bufferingBarEmptyBarBlendColor - A color to be blended with the graphical image specified in the emptyBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.
     * bufferingBarFilledBarBlendColor - This color will be blended with the graphical image specified in the filledBarImageUri field. The blending is performed by multiplying this value with each pixel in the image. If not changed from the default value, no blending will take place.  
<b>Retrieving Bar customization</b>
     * retrievingTextColor - Same as bufferingTextColor but for retrieving bar
     * retrievingBarEmptyBarImageUri - Same as bufferingBarEmptyBarImageUri but for retrieving bar
     * retrievingBarFilledBarImageUri - Same as bufferingBarFilledBarImageUri but for retrieving bar
     * retrievingBarTrackImageUri - Same as bufferingBarTrackImageUri but for retrieving bar
     * retrievingBarTrackBlendColor - Same as bufferingBarTrackBlendColor but for retrieving bar
     * retrievingBarEmptyBarBlendColor - Same as bufferingBarEmptyBarBlendColor but for retrieving bar
     * retrievingBarFilledBarBlendColor - Same as bufferingBarFilledBarBlendColor but for retrieving bar  
<b>BIF customization</b>
     * focusRingColor - a color to be blended with the image displayed behind individual BIF images displayed on the screen  
<b>Endcard view theme attributes</b>
     * buttonsFocusedColor - repeat button focused text color
     * buttonsUnFocusedColor - repeat button unfocused text color
     * buttonsfocusRingColor - repeat button background color  
<b>grid attributes</b>
     * rowLabelColor - grid row title color
     * focusRingColor - grid focus ring color
     * focusFootprintBlendColor - grid unfocused focus ring color
     * itemTextColorLine1 - text color for 1st row on endcard item
     * itemTextColorLine2 - text color for 2nd row on endcard item
     * timerLabelColor - Color of remaining timer


### <a id="videoview#sample"></a>Sample of usage:
    video = CreateObject("roSGNode", "VideoView")

    video.content = content
    video.jumpToItem = index
    video.control = "play"

    m.top.ComponentController.callFunc("show", {
        view: video
    })

    ' developer can observe video.endcardItemSelected to handle endcard selection
    ' video.currentIndex or video.currentItem fields can be used to track what was the last video after video closed.


___

## <a id="categorylistview"></a>CategoryListView
### <a id="categorylistview#extends"></a>Extends: [SGDEXComponent](#sgdexcomponent)
### <a id="categorylistview#description"></a>Description
CategoryListView represents SGDEX category list view that shows two lists: one for categories another for items in category

### <a id="categorylistview#interface"></a>Interface
#### <a id="categorylistview#fields"></a>Fields
  
* <a id="categorylistview#fields#initialposition"></a>**initialPosition** (vector2d)
    * Default value: [0, 0]
    * Tells where set initial focus on itemsList: 1st coordinate = category, 2st coordinate = item in this category  
  
* <a id="categorylistview#fields#selecteditem"></a>**selectedItem** (vector2d)
    * Array with 2 ints - section and item in current section that was selected  
    * Read Only  
* <a id="categorylistview#fields#focuseditem"></a>**focusedItem** (int)
    * Current focued item index (within all categories)  
    * Read Only  
* <a id="categorylistview#fields#focuseditemincategory"></a>**focusedItemInCategory** (int)
    * Current focued item index from current focusedCategory  
  
* <a id="categorylistview#fields#focusedcategory"></a>**focusedCategory** (int)
    * Current focused category index.  
  
* <a id="categorylistview#fields#jumptoitem"></a>**jumpToItem** (int)
    * Jumps to item in items list (within all categories).  
  This field must be set after setting the content field.  
    * Write Only  
* <a id="categorylistview#fields#animatetoitem"></a>**animateToItem** (int)
    * Animates to item in items list (within all categories).  
    * Write Only  
* <a id="categorylistview#fields#jumptocategory"></a>**jumpToCategory** (int)
    * Jumps to category.  
    * Write Only  
* <a id="categorylistview#fields#animatetocategory"></a>**animateToCategory** (int)
    * Animates to category.  
    * Write Only  
* <a id="categorylistview#fields#jumptoitemincategory"></a>**jumpToItemInCategory** (int)  
    * Write Only  
* <a id="categorylistview#fields#animatetoitemincategory"></a>**animateToItemInCategory** (int)
    * Animates to item in current category.  
    * Write Only  
* <a id="categorylistview#fields#theme"></a>**theme** (assocarray)
    * Theme is used to change color of grid view elements  
Note. you can set TextColor and focusRingColor to have generic theme and only change attributes that shouldn't use it.  
Possible fields:  
*TextColor - changes color for all text fields in category list  
*focusRingColor - changes color of focus rings for both category and item list  
*categoryFocusedColor - set focused text color for category  
*categoryUnFocusedColor - set unfocused text color for category  
*itemTitleColor - set item title color  
*itemDescriptionColor - set item description color  
*categoryfocusRingColor - set color for category list focus ring  
*itemsListfocusRingColor - set color for item list focus ring  
  
* <a id="categorylistview#fields#content"></a>**content** (node)
    * In order to build proper content node tree you have to stick to this model:  
Possible fields:  
Category fields:  
Title - Title that will be displayed for category name  
CONTENTTYPE - Must be set to SECTION  
HDLISTITEMICONURL - The image file for the icon to be displayed to the left of the list item label when the list item is not focused  
HDLISTITEMICONSELECTEDURL - The image file for the icon to be displayed to the left of the list item label when the list item is focused  
HDGRIDPOSTERURL - The image file for the icon to be displayed to the left of the section label when the View resolution is set to HD.  
Item List fields:  
title - Title to be shown  
description - Description for item, max 4 lines  
hdPosterUrl - image url for item  


### <a id="categorylistview#sample"></a>Sample of usage:
    CategoryList = CreateObject("roSGNode", "CategoryListView")
    CategoryList.posterShape = "square"

    content = CreateObject("roSGNode", "ContentNode")
    content.Addfields({
        HandlerCategoryList: {
            name: "CGSeasons"
        }
    })

    CategoryList.content = content
    CategoryList.ObserveField("selectedItem", "OnEpisodeSelected")

    ' this will trigger job to show this View
    m.top.ComponentController.CallFunc("show", {
        view: CategoryList
    })

    <b>Advanced loading logic</b>

    root = {
        HandlerConfigCategoryList: {
            _description: "Content handler that will be called to populate categories if they are not created"
            name: "Component name that extends [ContentHandler](#ContentHandler)"
            fields: {
                field: "any field that you want to set in this component when it's created"
            }
        }
        _comment: "categories"
        children: [{
            title: "Category that is prepopulated"
            contentType: "section"
            children: [{
                title: "Title to be shown"
                description: "Description for item, max 4 lines"
                hdPosterUrl: "image url for item"
            }]
            _optionalFields: "Fields that can be used for category"
            HDLISTITEMICONURL: "icon that will be shown left to category when not focused"
            HDLISTITEMICONSELECTEDURL: "icon that will be shown left to category title when focused"
            HDGRIDPOSTERURL: "icon that will be shown left to title in item list"
        }, {
            title: "Category that requires content to be loaded"
            contentType: "section"
            HandlerConfigCategoryList: {
                _description: "Content handler that will be called if there are no children in category"
                name: "Component name that extends [ContentHandler](#ContentHandler)"
                fields: {
                    field: "any field that you want to set in this component when it's created"
                }
            }
        }, {
            title: "Category that has children but they need metadata (non serial model)"
            contentType: "section"
            HandlerConfigCategoryList: {
                _description: "Content handler that will be called if "
                name: "Component name that extends [ContentHandler](#ContentHandler)"
                _note: "use bigger value for page size to improve performance"
                _mandatatory: "This field is required in order to work"

                pageSize: 2
                fields: {
                    field: "any field that you want to set in this component when it's created"
                }
            }
            children: [{title: "Title to be shown"}
                {title: "Title to be shown"}
                {title: "Title to be shown"}
                {title: "Title to be shown"}]
        }, {
            title: "Category has children but there are more items that can be loaded"
            contentType: "section"
            HandlerConfigCategoryList: {
                _description: "Content handler will be called when focused near end of category"
                name: "Component name that extends [ContentHandler](#ContentHandler)"
                _mandatatory: "This field is required in order to work"
                hasMore: true
                fields: {
                    field: "any field that you want to set in this component when it's created"
                }
            }
            children: [{
                title: "Title to be shown"
                description: "Description for item, max 4 lines"
                hdPosterUrl: "image url for item"
            }]
        }]
    }

    CategoryList = CreateObject("roSGNode", "CategoryListView")
    CategoryList.posterShape = "square"

    content = CreateObject("roSGNode", "ContentNode")
    content.update(root)

    CategoryList.content = content

    ' this will trigger job to show this View
    m.top.ComponentController.CallFunc("show", {
        view: CategoryList
    })



___

## <a id="sgdexcomponent"></a>SGDEXComponent
### <a id="sgdexcomponent#extends"></a>Extends: Group
### <a id="sgdexcomponent#description"></a>Description
Base component for SGDEX views that adds common fields and handles theme params passing to view,  
 each view is responsible for populating proper params to it's views

### <a id="sgdexcomponent#interface"></a>Interface
#### <a id="sgdexcomponent#fields"></a>Fields
  
* <a id="sgdexcomponent#fields#theme"></a>**theme** (assocarray)
    * Theme is used to set view specific theme fields, this is used to set initial theme, if you want to update any value use updateTheme  
Commmon attributes for all view:  
*textColor - Set text color to all supported labels  
*focusRingColor - Set focus ring color  
*progressBarColor - Set color for progress bars  
*backgroundImageURI - Set url to background image  
*backgroundColor - Set background color  
*busySpinnerColor - Set loading spinner color  
*OverhangTitle - text that will be displayed in overhang title  
*OverhangTitleColor - Color of overhang title  
*OverhangShowClock - toggle showing of overhang clock  
*OverhangShowOptions - show options on overhang  
*OverhangOptionsAvailable - tells if options are available. Note this is only visual field and doesn't affect if developer implements options  
*OverhangVisible - set if overhang should be visible  
*OverhangLogoUri - url to overhang logo  
*OverhangBackgroundUri - overhang background url  
*OverhangOptionsText - text that will be show in options  
*OverhangHeight - height of overhang  
*OverhangBackgroundColor - overhang background color  
Usage:  
To set global theme attributes refer to [BaseScene](#basescene)  
To set view specific fields use:  
view = CreateObject("roSGNode", "GridView")  
view.theme = {  
textColor: "FF0000FF"  
}  
  
* <a id="sgdexcomponent#fields#updatetheme"></a>**updateTheme** (assocarray)
    * updateTheme is used to update view specific theme fields  
Usage is same as [theme](#sgdexcomponent) field but here you should only set field that you want to update  
If you want global updates use [BaseScene updateTheme](#basescene)  
  
* <a id="sgdexcomponent#fields#style"></a>**style** (string)
    * is used to tell view what style should be used, style is view specific  
  
* <a id="sgdexcomponent#fields#postershape"></a>**posterShape** (string)
    * is used to tell view what poster shape should be used for posters that are rendered on view  
  
* <a id="sgdexcomponent#fields#content"></a>**content** (node)
    * Main field for setting content  
content tree is specific to each view and is handled by view itself  
  
* <a id="sgdexcomponent#fields#close"></a>**close** (boolean)
    * Control field to tell View Manager to close this View manually.  
Is desined for authentication flows or other flows when set of Views should be closed after some action.  
  
* <a id="sgdexcomponent#fields#wasclosed"></a>**wasClosed** (boolean)
    * Observe this to know when view is closed and removed from View Manager  
  
* <a id="sgdexcomponent#fields#savestate"></a>**saveState** (boolean)
    * Observe this to know when view is hiding and new top view is being opened  
  
* <a id="sgdexcomponent#fields#wasshown"></a>**wasShown** (boolean)
    * Observe this to know when view was shown for first time or restored after top view was closed  


___

