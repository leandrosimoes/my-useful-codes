# **JS**
Some JS useful codes:

### Order an array of objects by another array of values and property name

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

### Get video ID and thumbnail from Youtube url

```javascript
//Return the youtube video ID
function getYoutubeVideoID(url) {
    if (!url || url.indexOf('yout') === -1) return null;

    var videoId = url.split('v=')[1];
    var ampersandPosition = videoId.indexOf('&');
    if (ampersandPosition != -1) {
        videoId = videoId.substring(0, ampersandPosition);
    }

    return videoId;
}

//Return the url of youtube video thumbnail
function getYoutubeThumbnail(url, index) {
    if (!url || url.indexOf('yout') === -1) return null;
    
    index = parseInt(index) || 0; //You can pass the index of thumbnail if you know

    var videoId = getYoutubeVideoID(url); //Here I'm using the function that I show bellow

    if (videoId.length !== 11) return null;
    
    return 'https://img.youtube.com/vi/' + videoId + '/' + thumbnailIndex + '.jpg';
}
```

----------

### Create a BLOB from base64 string

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

```javascript
function jsonToURI(obj) {
    return Object.keys(obj).map(function (key) {
        return encodeURIComponent(key) + '=' + encodeURIComponent(obj[key]);
    }).join('&');
}
```

### Vanilla function to extend objects

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
