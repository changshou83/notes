```js
// 统计字符频率柱状图
class DefaultMap extends Map {
	constructor(defaultValue) {
		super();
		this.defaultValue = defaultValue;
	}
	get(key) {
		return super.has(key)
			? super.get(key)
			: this.defaultValue;
	}
}

class Histogram {
	constructor() {
		this.letterCounts = new DefaultMap(0); // 字母到数量的映射
		this.totalLetters = 0;
	}
	add(text) {
		text = text.replace(/\s/g, '').toUpperCase();
		for(const char of text) {
			const count = this.letterCounts.get(char);
			this.letterCounts.set(char, count+1);
			this.totalLetters++;
		}
	}
	toString() {
		const entries = [...this.letterCounts];
		
		entries.sort((a, b) => {
			const [achar, acharCnt] = a;
			const [bchar, bcharCnt] = b;
			
			if(acharCnt === bcharCnt) {
				return achar < bchar ? -1 : 1; // 按字母表排序
			} else {
				return bcharCnt - acharCnt; // 降序排序
			}
		});
		
		for(const entry of entries) {
			entry[1] = entry[1] / totalLetters * 100;
		}
		
		entries = entries.filter((entry) => entry[1] >= 1);

		const lines = entries.map(([char, cnt]) => `${char}: ${'#'.repeat(Math.round(cnt))} ${cnt.toFixed(2)}%`);
		
		return lines.join('\n');
	}
}

async function histogramFromStdin() {
	process.stdin.setEndocing('uft-8');
	
	const histogram = new Histogram();
	for await (const chunk of process.stdin) {
		histogram.add(chunk);
	}
	return histogram;
}
histogramFromStdin()
	.then(histogram => console.log(histogram.toString()));
```
