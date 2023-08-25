
- 函子使得函数可以处理不同结构的值
- Monad可以组合处理不同类型的函数

```js
import * as fs from 'fs/promises';
import * as url from 'node:url';
import * as http from 'node:http';
import * as sys from 'node:sys';

// 管道，类型为   Promise a -> (a -> b) -> Promise b
const pipe = (val, fns) => {
	return fns.reduce((ret, fn) => bind(ret,fn),val);
}
// 函子，类型为   a -> Promise a
const unit = val => Promise.resolve(val);
// Monad，类型为 Promise a -> (a -> b) -> Promise b
const bind = (input, fn) => {
	return input
		.then(x => fn(x))
		.then(y => y);
}

// 业务
const readFile = (path) => fs.readFile(path);
const getUrl = (json) => {
	return Promise.resolve(
		url.parse(JSON.parse(json).url)
	);
}
const httpGet = (uri) => {
	const client = http.createClient(80, uri.hostname);
	const request = client.request(
		'GET',
		uri.pathname,
		{
			'Host': uri.hostname
		}
	)
	
	return new Promise((resolve) => {
		request.addListener('response', res => resolve(res));
		request.end();
	});
}
const responseBody = (response) => {
	return new Promise((resolve) => {
		let body = '';
		
		response.addListener('data', c => body += c);
		response.addListener('end', () => resolve(body));
	});
}
const print = (string) => new Promise(sys.puts(string));

pipe(
	unit(__dirname + '/urls.json'), // string -> Promise string
	[
		readFile,     // string   -> Promise string
		getUrl,       // string   -> Promise URI
		httpGet,      // URI      -> Promise Response
		responseBody, // Response -> Promise string
		print         // string   -> Promise null
	]
)
```
