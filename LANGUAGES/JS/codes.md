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

### Create thumbnail BLOB from HTML5 `<video>` tag

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

