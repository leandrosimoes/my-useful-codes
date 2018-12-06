# **JS**
Some JS useful codes:

### Order an array of objects by another array of values and property name
##### I used this code when I needed to order an array passing another array with the same properties but in some order

```javascript
function orderArray(arr, order, propName) {
    var orderedArray = [];
    
    for(i=0;i<order.length;i++) {
        //Filter array items by the current order 
        //in for loop
        var filtered = arr.filter(function(obj) {
            return obj[propName] !== undefined && obj[propName] == order[i]; 
        });

        //If item was found, push it into the 
        //return array
        if(!!filtered && filtered.length > 0) {
            for(j=0;j<filtered.length;j++) {
                orderedArray.push(filtered[j]);
            }
        }
    }

    //Push items from the original array that 
    //is not inside the order array into the 
    //return array
    for(k=0;k<arr.length;k++) {
        if(orderedArray.indexOf(arr[k]) == -1) {
            orderedArray.push(arr[k]);
        }
    }
    
    return orderedArray;
}
```

### Samples:

```javascript
var arrayToBeOrdered = [{ name: 'A' }, { name: 'B' }, { name: 'C' }]
var arrayWithTheOrderValues = ['C', 'A', 'B'];

orderArray(arrayToBeOrdered, arrayWithTheOrderValues, 'name'); // [{ name: 'C', 'A', 'B' }];

// Another tests
orderArray(arrayToBeOrdered, ['C'], 'name'); // [{ name: 'C' }, { name: 'A' }, { name: 'B' }];
orderArray(arrayToBeOrdered, ['C','A'], 'name'); // [{ name: 'C' }, { name: 'A' }, { name: 'B' }];
orderArray(arrayToBeOrdered, ['C','B', 'A'], 'name');  // [{ name: 'C' }, { name: 'B' }, { name: 'A' }];
orderArray(arrayToBeOrdered, ['C','A', 'B', 'D'], 'name');  // [{ name: 'C' }, { name: 'A' }, { name: 'B' }];
orderArray(arrayToBeOrdered, ['A','C'], 'name');  // [{ name: 'A' }, { name: 'C' }, { name: 'B' }];
orderArray(arrayToBeOrdered, ['D', 'C','A', 'B'], 'name'); // [{ name: 'C' }, { name: 'A' }, { name: 'B' }];
orderArray(arrayToBeOrdered, ['A', 'B','C', 'D'], 'name'); // [{ name: 'A' }, { name: 'B' }, { name: 'C' }];
orderArray(arrayToBeOrdered, ['Z', 'W','X', 'Z'], 'name'); // [{ name: 'A' }, { name: 'B' }, { name: 'C' }];
orderArray(arrayToBeOrdered, [], 'name'); // [{ name: 'A' }, { name: 'B' }, { name: 'C' }];

```

----------

### Verify Youtube URL
##### Verify if the url is a valid Youtube URL

```javascript
function isYouTubeUrl(url) {
    if (!url) return false;

    var regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=|\?v=)([^#\&\?]*).*/;
    var match = url.match(regExp);
    if (match && match[2].length == 11) {
        return true;
    } else {
        return false;
    }
}
```

----------

### Get Youtube video ID
##### This code I use to get the Youtube video ID passing the video url
```javascript
function getYoutubeVideoID(url) {
    if (!isYouTubeUrl(url)) return null; //I'm using the code that I presented bellow

    var videoId = url.split('v=')[1];
    var ampersandPosition = videoId.indexOf('&');
    if (ampersandPosition != -1) {
        videoId = videoId.substring(0, ampersandPosition);
    }

    return videoId;
}
```

----------

### Get Youtube video thumbnail
##### This code I use to get the Youtube video thumbnail passing the video url
```javascript
function getYoutubeThumbnail(url, index) {
    if (!isYouTubeUrl(url)) return null; //I'm using the code that I presented bellow
    
    index = parseInt(index) || 0; //You can pass the index of thumbnail if you know

    var videoId = getYoutubeVideoID(url); //I'm using the code that I presented bellow

    if (videoId.length !== 11) return null;
    
    return 'https://img.youtube.com/vi/' + videoId + '/' + thumbnailIndex + '.jpg';
}
```

----------

### Append Youtube `<iframe>`
##### Append and Youtube embedded iframe on page

```javascript
function appendYoutubeVideo(src, width, height, locationId) {
	if(!isYoutubeUrl(src)) throw 'src is required'; //I'm using the code that I presented bellow
    
    var videoId = getYoutubeVideoID(src), //I'm using the code that I presented bellow
        newSrc = 'https://www.youtube.com/embed/' + videoId,
        location = document.getElementById(locationId) || document.body;
	
	var iframe = document.createElement('iframe');
	iframe.id = 'yt-iframe-' + new Date().getTime();
	iframe.width = width || 560;
	iframe.height = height || 349;
	iframe.src = newSrc;
    iframe.frameBorder = 0;
	
	location.appendChild(iframe);
}
```

----------

### Verify Vimeo URL
##### Verify if the url is a valid Vimeo URL

```javascript
function isVimeoUrl(url) {
    if(!url) return false;

    return /^(http\:\/\/|https\:\/\/)?(www\.)?(vimeo\.com\/)([0-9]+)$/.test(url);
}
```

----------

### Get Vimeo video ID
##### This code I use to get the Vimeo video ID passing the video url
```javascript
function getVimeoVideoId(url) {
    if (!isVimeoUrl(url)) return null;

    return url.match(/\d{9}/);
}
```

----------

### Get Vimeo video data
##### This code I use to get the Vimeo video data (like thumbnails url, author, etc) passing the video url
```javascript
function vimeoLoadingData(url) {
    if(!isVimeoUrl(url)) return null;

    var videoId = getVimeoVideoId(url),
        url = "http://vimeo.com/api/v2/video/" + videoId + ".json";
        
    $.ajax({
        type:'GET',
        url: url,
        dataType: 'json',
        success: function(data) {
            if(!!data && data.length > 0) {
                return data[0];
            } else {
                return null;
            }
        }
    });
}
```

### Sample Data:
```json
{
    "id":999999999,
    "title":"Video Title",
    "description":"Video Description",
    "url":"https://vimeo.com/999999999",
    "upload_date":"2016-12-25 16:30:55",
    "thumbnail_small":"https://i.vimeocdn.com/video/999999999_100x75.jpg",
    "thumbnail_medium":"https://i.vimeocdn.com/video/999999999_200x150.jpg",
    "thumbnail_large":"https://i.vimeocdn.com/video/999999999_640.jpg",
    "user_id":2417485,"user_name":"John Doo","user_url":"https://vimeo.com/johndoo",
    "user_portrait_small":"https://i.vimeocdn.com/portrait/9999999_30x30",
    "user_portrait_medium":"https://i.vimeocdn.com/portrait/9999999_75x75",
    "user_portrait_large":"https://i.vimeocdn.com/portrait/9999999_100x100",
    "user_portrait_huge":"https://i.vimeocdn.com/portrait/9999999_300x300",
    "stats_number_of_likes":629,
    "stats_number_of_plays":76849,
    "stats_number_of_comments":60,
    "duration":254,
    "width":1920,
    "height":1080,
    "tags":"Tag1, Tag2, TagN",
    "embed_privacy":"anywhere"
}
```

----------

### Append Vimeo `<iframe>`
##### Append and Vimeo embedded iframe on page

```javascript
function appendVimeoVideo(src, width, height, locationId) {
    if(!isVimeoUrl(src)) throw 'src is required';

    var videoId = getVimeoVideoId(src),
        newSrc = 'https://player.vimeo.com/video/' + videoId + '?color=ffffff',
        location = document.getElementById(locationId) || document.body;
      
    var iframe = document.createElement('iframe');
    iframe.id = 'yt-iframe-' + new Date().getTime();
    iframe.width = width || 640;
    iframe.height = height || 360;
    iframe.src = newSrc;
    iframe.frameBorder = 0;
	iframe.webkitallowfullscreen = true;
	iframe.mozallowfullscreen = true; 
    iframe.allowfullscreen = true;

    location.appendChild(iframe);
}
```

----------

### Create a BLOB from base64 string
##### Used to create a Blob object passing a base64 string
```javascript
function b64toBlob(b64Data, contentType, sliceSize) {
    contentType = contentType || '';
    sliceSize = sliceSize || 512;

    var byteCharacters = atob(b64Data);
    var byteArrays = [];

    for (var offset = 0; offset < byteCharacters.length; offset += sliceSize) {
        var slice = byteCharacters.slice(offset, offset + sliceSize);

        var byteNumbers = new Array(slice.length);
        for (var i = 0; i < slice.length; i++) {
            byteNumbers[i] = slice.charCodeAt(i);
        }

        var byteArray = new Uint8Array(byteNumbers);

        byteArrays.push(byteArray);
    }

    var blob = new Blob(byteArrays, { type: contentType });
    return blob;
}
```

----------

### Create thumbnail BLOB from HTML5 `<video>` tag (Needs JQuery)
##### Create a Blob object with a thumnail image of a html5 `video` tag
```javascript
function createVideoThumb() {
    try {
        var canvas = document.createElement('canvas'),
            video = $('video')[0],
            $video = $(video),
            w = $video.width(),
            h = $video.height();

        canvas.width = w;
        canvas.height = h;
        canvas.style.width = w + "px";
        canvas.style.height = h + "px";
        canvas.style.position = "absolute";
        canvas.style.display = 'none';
        canvas.id = 'temp-canvas';

        var body = document.getElementsByTagName("body")[0];
        body.appendChild(canvas);

        canvas.getContext('2d').drawImage(video, 0, 0, w, h);

        canvas.toBlob(function (blob) {
            $(canvas).remove();
            
            return blob;
        }, "image/jpeg", 1);
    } catch (e) {
        console.log('Error!.')
    }
}
```

----------

### String format like .NET String.Format
##### I used this string.format a lot on .NET code, so I just create the same for javascript
```javascript
function stringFormat(args) {
    var str = this;

    if (!str || !args || typeof str != 'string') return str;

    for (var i = 0; i < args.length; i++) {
        str = str.replace('{' + i + '}', args[i]);
    }

    return str;
};
```

Than you can add to string prototype like this:

```javascript
String.prototype.stringFormat = stringFormat;
```

And use like this:

```javascript
'my {0} to {1}'.format(['string', 'format']); //my string to format
```

----------

### JSON object to URI encoded params
##### Encode a json object properties to be passed in url
```javascript
function jsonToURI(obj) {
    return Object.keys(obj).map(function (key) {
        return encodeURIComponent(key) + '=' + encodeURIComponent(obj[key]);
    }).join('&');
}
```

### Vanilla function to extend objects
##### This function merges objects like $.extends of JQuery
```javascript
function extend() {
    var extended = {};
    var deep = false;
    var i = 0;
    var length = arguments.length;

    // Check if a deep merge
    if (Object.prototype.toString.call(arguments[0]) === '[object Boolean]') {
        deep = arguments[0];
        i++;
    }

    // Merge the object into the extended object
    var merge = function (obj) {
        for (var prop in obj) {
            if (Object.prototype.hasOwnProperty.call(obj, prop)) {
                // If deep merge and property is an object, merge properties
                if (deep && Object.prototype.toString.call(obj[prop]) === '[object Object]') {
                    extended[prop] = extend(true, extended[prop], obj[prop]);
                } else {
                    extended[prop] = obj[prop];
                }
            }
        }
    };

    // Loop through each object and conduct a merge
    for (; i < length; i++) {
        var obj = arguments[i];
        merge(obj);
    }

    return extended;

}
```

-----------

### Parsing HTML string with pure vanilla JS
##### This is a vanilla html parser that can be used with multiline html string
```javascript
function parseHtml(html) {
    var wrapMap = {
        option: [1, "<select multiple='multiple'>", "</select>"],
        legend: [1, "<fieldset>", "</fieldset>"],
        area: [1, "<map>", "</map>"],
        param: [1, "<object>", "</object>"],
        thead: [1, "<table>", "</table>"],
        tr: [2, "<table><tbody>", "</tbody></table>"],
        col: [2, "<table><tbody></tbody><colgroup>", "</colgroup></table>"],
        td: [3, "<table><tbody><tr>", "</tr></tbody></table>"],
        body: [0, "", ""],
        _default: [1, "<div>", "</div>"]
    };
    wrapMap.optgroup = wrapMap.option;
    wrapMap.tbody = wrapMap.tfoot = wrapMap.colgroup = wrapMap.caption = wrapMap.thead;
    wrapMap.th = wrapMap.td;
var match = /<\s*\w.*?>/g.exec(html);
    var element = document.createElement('div');
    if (match != null) {
        var tag = match[0].replace(/</g, '').replace(/>/g, '').split(' ')[0];
        if (tag.toLowerCase() === 'body') {
            var dom = document.implementation.createDocument('http://www.w3.org/1999/xhtml', 'html', null);
            var body = document.createElement("body");
            // keeping the attributes
            element.innerHTML = html.replace(/<body/g, '<div').replace(/<\/body>/g, '</div>');
            var attrs = element.firstChild.attributes;
            body.innerHTML = html;
            for (var i = 0; i < attrs.length; i++) {
                body.setAttribute(attrs[i].name, attrs[i].value);
                }
                return body;
            } else {
                var map = wrapMap[tag] || wrapMap._default, element;
                html = map[1] + html + map[2];
                element.innerHTML = html;
                // Descend through wrappers to the right content
                var j = map[0] + 1;
                while (j--) {
                    element = element.lastChild;
                }
            }
        } else {
            element.innerHTML = html;
            element = element.lastChild;
        }
        
    return element;
}
```

### References: [#1](http://krasimirtsonev.com/blog/article/Revealing-the-magic-how-to-properly-convert-HTML-string-to-a-DOM-element)

-----------

### Copy to clipboard (Needs JQuery)
##### Yeah, now we can copy to clipboard again using execCommand
```javascript
function copyToClipboard(contentToCopy) {
    if(!document.execCommand) throw 'Your browser do not support this feature.'; //Feature detection
    
    $('body').append('<textarea class="cliparea"></textarea>');
    
    var $cliparea = $('.cliparea');
    $cliparea.val(contentToCopy || '');
    $cliparea.focus();
    $cliparea.select();

    document.execCommand('Copy');

    $cliparea.remove();
}
```

-----------

### Check if your server is online
##### This is just a little trick to check if your server is oline
```javascript
function isServerOnline(callbackOnline, callbackOffline) {
	var img = document.body.appendChild(document.createElement("img"));
	img.style.display = 'none';
	img.onload = function() {
	    if (!!callbackOnline && typeof callbackOnline == 'function') callbackOnline();
	    
	    img.parentElement.removeChild(img);
	};
	img.onerror = function() {
	    if (!!callbackOffline && typeof callbackOffline == 'function') callbackOffline();
	    
	    img.parentElement.removeChild(img);
	};
	
	//You have to put this image on your server to try to make the download
	//I suggest you put a very small one, just to check if the download completes or not
	img.src = "http://localhost:51604/online-check.png?" + new Date().getTime();
}
```
-----------

### Format string with any first letters words to uppercase
##### I use this function to format strings with all first letter of any word in uppercase.

```javascript
/*
Usualy I use this to format names, so if I pass "NEW YORK", 
the function will return "New York"
*/
function anyFirstLetterToUpperCase(str) {
    if(!str) return '';
    
	var parts = str.split(' ');
	
	str = '';
	for(var i in parts) {	
		var letters = parts[i].split('');
		
		str += letters[0].toUpperCase();
		
		letters = letters.slice((letters.length - 1) * -1);
		for(var j in letters) {
			if(j === 0) continue;
			
			str += letters[j].toLowerCase();
		}
		
		str += ' ';
	}
	
	return str.trim();
};
```

-----------

### Abreviate person names
##### I use this function to short some names that I return from a third part api for example

```javascript
/*
Enum of short name formats
	Samples using this name: "LEANDRO SIMÕES DA SILVA"
*/		
var shortMethodEnum = {
	firstOnly: 0, // Leandro
	lastCommaFirst: 1, // Silva, Leandro
	firstDotsLast: 2, // Leandro S. D. Silva
	firstAndLast: 3 // Leandro Silva
};

/* 	
Params:
	name: String of name that you want to get short
	shorMethod: Use de "shorMethodEnum" above to especify the format of the short name (Default is "firstOnly")
	namesToExclude: String of names that you want to exclude from the original name before get shorted

Usage: 
	I have to short the name "Leandro Simões da Silva" and I use this function to get short forms like:
		"Leandro", "Silva, Leandro", "Leandro S. D. Silva", "Leandro Silva" etc.
	If I pass "da" in "namesToExclude" param I can get the name like "Leandro S. Silva" for example.
*/
function shortName(name, shortMethod, namesToExclude) {
    if(!name) return '';
	
	name = anyFirstLetterToUpperCase(name); // WARNING: Here I'm using the method above
	
	if(!!namesToExclude && namesToExclude.length > 0) {
		for(var i in namesToExclude) {
			name = name.replace(new RegExp(namesToExclude[i], 'g'), '').replace(/  /g, ' ').trim();
		}
	}
	      
	var parts = name.split(' '),
		qtyParts = parts.length;
		
	// WARNING: Here I use the enum of short methods above
	if(!shortMethod || shortMethod === shortMethodEnum.firstOnly) {
		name = parts[0];
	} else if(shortMethod === shortMethodEnum.lastCommaFirst) {
		if(qtyParts >= 2) {
			name = parts[qtyParts - 1] + ', ' + parts[0];
		}
	} else if(shortMethod === shortMethodEnum.firstDotsLast) {
		if(qtyParts > 1) {
			name = '';
			
			for(i in parts) {
				if(i > 0 && i < (parts.length - 1)) {
					name += parts[i].split('')[0] + '. ';
				} else {
					name += parts[i] + ' ';
				}
			}
		}
	} else if(shortMethod === shortMethodEnum.firstAndLast) {			
		if(qtyParts > 2) {
			name = parts[0] + ' ' + parts[qtyParts - 1];
		}
	}
	
	return name.trim();
};
```

-----------

### Get the default language of browser
##### I use this function to get the browser default language and then can use in localization or something like that

```javascript
function getDefaultBrowserLanguage() {
	var returnLanguage = '';
	if (!!navigator.languages && navigator.languages instanceof Array && navigator.languages.length > 0) {
		returnLanguage = navigator.languages[0];
	} else {
		returnLanguage = navigator.language || navigator.browserLanguage;
	}
	
	return returnLanguage || 'en-US'; // Here you can set any default language if any was found, in my case is 'en-US'
};
```

-----------

### Get the used local and session storage used size
##### I use this function to know how much memory I already used of local and session storage

```javascript
function getLocalStorageUsedSize() {
	var total = 0,
       	    size = 0;

	for (var x in localStorage) {
		size = ((localStorage[x].length + x.length) * 2);
		total += size;
	};
    
	return (total / 1024);
}

function getSessionStorageUsedSize() {
	var total = 0,
       	size = 0;

	for (var x in sessionStorage) {
		size = ((sessionStorage[x].length + x.length) * 2);
		total += size;
	};
    
	return (total / 1024);
}
```

-----------

### Validate a GUID string
##### I use this function to validate a GUID string

```javascript
function isGuid(text) {
	if(!text) return false;
	
	var reg = new RegExp('^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$'),
	    matches = text.match(reg);
    	
	return !!matches && matches.length > 0;
}
```

-----------

### Generate a GUID string
##### I use this function to generate GUID string

```javascript
function guid() {
  function s4() {
    return Math.floor((1 + Math.random()) * 0x10000)
      .toString(16)
      .substring(1);
  }
  return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
}
```

### References: [#1](https://stackoverflow.com/a/105074/1988289)

-----------

### Convert temperature between Fahrenheit and Celsius
##### I use this function when I need to convert temperatures between Fahrenheit and Celsius

```javascript
function celciusToFahrenheit(celciusDegrees) {
    return ((celciusDegrees / (5/9)) +32);
}
function fahrenheitToCelcius(fahrenheitDegrees) {
    return (fahrenheitDegrees - 32) * (5/9);
}
```

-----------

### Countdown days
##### I use this function to count how many days left to a specific date

```javascript
function daysLeft(date1, date2) {
    return Math.ceil((date2 - date1) / 1000 / 60 / 60 / 24)
}
```

-----------

### Leap year
##### I use this function to verity if a specific year is a leap year

```javascript
function isLeapYear(year = new Date().getFullYear()) {
    return (year % 100 === 0) ? (year % 400 === 0) : (year % 4 === 0);
}
```

-----------

### RGB 2 HEX
##### I use this function to convert RGBA colors to HEX colors

```javascript
var hexDigits = new Array("0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "a", "b", "c", "d", "e", "f");

function hex(x) {
    return isNaN(x) ? "00" : hexDigits[(x - x % 16) / 16] + hexDigits[x % 16];
}

function rgb2hex(rgb) {
    rgb = rgb.match(/^rgb\((\d+),\s*(\d+),\s*(\d+)\)$/);
    return "#" + hex(rgb[1]) + hex(rgb[2]) + hex(rgb[3]);
}
```

-----------

### Async For Each
##### A async implementation of javascript forEach

```javascript
const asyncForEach = async (array, callback) => {
	for (let index = 0; index < array.length; index++) {
		await callback(array[index], index, array);
	}
}

const YOUR_ARRAY = []

// THEN USE LIKE THIS
await asyncForEach(YOUR_ARRAY, async array_item => {
	...
})
```
