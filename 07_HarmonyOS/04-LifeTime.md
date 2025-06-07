# LifeTime

```ets
[初次渲染] aboutToAppear ——> onDidBuild ——> onPageShow
[切后台]   onPageHide
[切前台]	onPageShow
[杀后台]	onPageHide ——> aboutToDisappear
[返回]	 onBackPress
```

