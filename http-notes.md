# HTTP Notes

## What happens when a user agent makes a request?
For computers to communicate, they need to be connected. You can't just be plugging cables everywhere though.

Every computer on a network is connected to a *router*, a special computer which has one job: making sure that a message sent from a given computer arrives at the right destination computer.

### The Internet

Routers can be connected together. So, by connecting computers to routers, and routers to routers, you can scale infinitely. But you can't run cables between every house - instead, existing infrastructure is used: telephone lines.

To connect the router to the telephone infrastructure, we use a *modem*, which turns the information from the network into information that can be managed by the telephone infrastructure, and vice versa.

Now that we're connected to the phone lines, we use an ISP: a company that manages special routers that are linked together and can access other ISP's routers. So messages from our home network can travel via these to any destination router on this network.

### Finding computers

Any computer linked to a network has a unique address that identifies it, an IP (Internet Protocol) address, made of 4 numbers separated by dots (e.g. 192.168.4.20). These IP addresses are represented by domain names to make them easier to remember.

### Internet vs the web

The Internet is a technical infrastructure, whereas the Web is a service built on top of the infrastructure. Examples of other services built on top of the Internet: email, IRC.

#### Intranets/Extranets

Intranets are private networks, restricted to members of a particular organisation. Commonly used to provide a portal for organisation members to securely access shared resources/collaborate.

Extranets are similar to Intranets, but they open all or part of a private network to allow sharing and collaboration with other organisations. Commonly used for safely and securely sharing information with clients and stakeholders.

## Headers

Request headers (in the form of tuples) are information about the request you're making. They provide context about the request to the server, so the server can tailor the response.
e.g. API keys, Accept.

## Promise

    const promise = new Promise((resolve, reject) => {  });

Promises allow you to "avoid callback hell". Fetch uses promises by default.

## XMLHTTPRequest API

Sometimes, you'll get an unsuccessful response, but it will trigger a successful response on your end. This is because the "error" was just data sent as part of the technically "successful" server response. So if your network is not responding or something like that, xhr.error might recognise that as an unsuccessful response, but if the server responds at all it won't trigger.

So, use 

    xhr.onload = () => {
        if (xhr.status >= 400) {
            reject(xhr.response);
        } else {
            resolve(xhr.response);
        }
    };

Reject will forward the response description (which you may want) down the promise chain as an error, which will then end up in `.catch`

## fetch() API

Relatively modern addition, may not be supported by very old browsers. 

`fetch('url.com');` without any additional arguments will send a GET request.

By default, fetch uses promises, so you can chain promise methods.

`response.json()` and `response.text()` for automatic response parsing. These also return promises so you can chain if you `return` them.

### Adding headers

    const sendHttpRequest = (method, url, data) => {
    return fetch(url, {
            method: method,
            body: JSON.stringify(data),
            // Setting headers: if data is truthy (if there is data),
            // add header content-type, else empty object
            headers: data ? { 'Content-Type': 'application/json' } : {}
        }).then(response => {
            return response.json();
        });
    }

You can append as many headers as you want to `headers` in the `fetch` arguments object, as a key pair value (there is a ternary operator used in this example as well).

### Handling server errors

Same thing as above, where a successful response has a non-success status code, but is treated by the API as a success case.

In fetch, `response` objects have a status code attached, `response.status`. You can use this in a similar way to check the status code, e.g. `if (response.status >= 400)`. You can also use `!response.ok` apparently?

You can `throw error;` inside a fetch, which will reject the promise chain, with `throw new Error('Something went wrong!)`.

But if you want to add any additional content to the error, then that would be your response body. BUT you can't access the response body in this step, you have to parse it (like at the end, with `return response.json();`). The solution to this is to do a NESTED promise, which normally isn't great, but here it's a good solution.

    const sendHttpRequest = (method, url, data) => {
        return fetch(url, {
            method: method,
            body: JSON.stringify(data),
            // Setting headers: if data is truthy (if there is data),
            // add header content-type, else empty object
            headers: data ? { 'Content-Type': 'application/json' } : {}
        }).then(response => {
            if (response.status >= 400) { 
                // return will add this to the promise chain.
                return response.json().then(errResData => {
                    const error = new Error('Something went wrong!');
                    error.data = errResData;
                    throw error;
                })
            } // Because we used return, no need for else.
            return response.json();
        });
    }

So now we're either returning the body data (resolve), or an error (reject). 

    const sendData = () => {
        sendHttpRequest('POST', 'https://reqres.in/api/register', {
            email: "eve.holt@reqres.in",
            //password: "pistol"
        }).then(responseData => {
            console.log(responseData);
        }).catch(err => {
            console.log('err ' + err);
            console.log('error data ' + err.data);
        });
    };

You could also access the stored error.data (from the argument errResData, which stands for "error response data"), inside `catch(err => { })` (as seen above).