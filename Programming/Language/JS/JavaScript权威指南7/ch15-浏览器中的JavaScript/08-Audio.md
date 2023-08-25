
- `new Audio()`，创建一个`audio`标签
- `WebAudio`，可以生成和播放合成音效

## `WebAudio` API 演示

```js
// 创建一个音频上下文
const ctx = new this.AudioContext();

// 定义基准声音为三个纯正弦波的组合
const notes = [293.7, 370.0, 440.0];
// 为每个想要播放的音符创建振荡器节点
const oscillators = notes.map(note => {
	const o = ctx.createOscillator();
	o.frequency.value = note;
	return o;
})

// 通过随时间控制音量来构造声音
const volumeControl = ctx.createGain();
volumeControl.gain.setTargetAtTime(1, 0.0, 0.02);
volumeControl.gain.setTargetAtTime(0, 0.1, 0.2);

// 用户的扬声器
const speakers = ctx.destination;
// 把每个源音符都连接到音量控制
oscillators.forEach(o => o.connect(volumeControl));
// 把音量控制的输出连接到扬声器
volumeControl.connect(speakers);

// 持续 1.25 秒
const startTime = ctx.currentTime;
const stopTime = startTime + 1.25;
oscillators.forEach(o => {
  o.start(startTime);
  o.stop(stopTime);
})

// 如果想要创建一系列声音，可以使用事件处理程序
oscillators[0].addEventListener('ended',() => {
	// 在音符停止播放时会调用这个事件处理程序，可以播放另一个声音
})
```
