
- Controller の value の変化に伴って、表示が切り替わるアイコンをレンダリングする

- デフォルトで用意されているアイコンは少ないので、希望するアイコンがない場合はLottie などのパッケージを利用すること


> [!NOTE] Lottie
> https://pub.dev/packages/lottie
> Lottie 公式から json ベースのアニメーションファイルをダウンロードし、それをLottieBuilder.aseet などから呼び出して簡単に使うことができる。
> 
> 再生時間等は、LottieBuilder に渡したController の duration に影響されるが、 onLoaded 関数内で compotision.duration を参照して、オリジナルの duration を取得することもできる。
> 
> 無料のものはそこまでクォリティーは高くないが、使えるアイコン・アニメーションの種類は豊富。
> もちろん有料でクォリティーが高いものもある。

#### コード例

```dart
// state 内
  late final AnimationController _playPauseController = AnimationController(
    vsync: this,
    duration: const Duration(
      milliseconds: 500,
    ),
  );

// build method 内
  GestureDetector(
	onTap: _onPlayPauseTap,
	child: Row(
	  mainAxisAlignment: MainAxisAlignment.center,
	  children: [
		AnimatedIcon(
		  icon: AnimatedIcons.pause_play,
		  progress: _playPauseController,
		  size: 60,
		),
		LottieBuilder.asset(
		  "assets/animations/play_lottie.json",
		  controller: _playPauseController,
		  onLoaded: (composition) {
			_playPauseController.duration = composition.duration;
		  },
		  width: 200,
		  height: 200,
		),
	  ],
	),
  )
```


#### 実際の挙動

左 : AnimatedIcon (default Icon)
右 : Lottie animation

![[AnimatedIcon_1.gif]]