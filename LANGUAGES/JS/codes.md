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
