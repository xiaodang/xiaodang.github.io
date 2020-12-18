---
title: 利用MutationObserver API追踪DOM的修改
date: 2020-12-18 21:50:59
tags: Web
---
之前写爬虫的时候，经常需要从界面入手，找到自己想要的数据，然后看网络请求，来搜索是哪个api返回的数据，一般情况下，都能从网络请求中发现想要的数据。但是当数据做了混淆之后，很多数据是没有办法直接从api中搜索到的。这时候就可以用到`MutationObserver` API。

MDN上的描述`The MutationObserver interface provides the ability to watch for changes being made to the DOM tree. `。简单翻译一下就是，这个接口提供了监控DOM树变化的能力。


### MDN的例子
```
// Select the node that will be observed for mutations
const targetNode = document.getElementById('some-id');

// Options for the observer (which mutations to observe)
const config = { attributes: true, childList: true, subtree: true };

// Callback function to execute when mutations are observed
const callback = function(mutationsList, observer) {
    // Use traditional 'for loops' for IE 11
    //如果需要调试的话，只需要在callback里面执行debugger就可以了
    for(const mutation of mutationsList) {
        if (mutation.type === 'childList') {
            console.log('A child node has been added or removed.');
        }
        else if (mutation.type === 'attributes') {
            console.log('The ' + mutation.attributeName + ' attribute was modified.');
        }
    }
};

// Create an observer instance linked to the callback function
const observer = new MutationObserver(callback);

// Start observing the target node for configured mutations
observer.observe(targetNode, config);

// Later, you can stop observing
observer.disconnect();
```

