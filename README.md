# 共享儿女：一次关于信任的服务体验

《共享儿女》是一个纯前端、手机端竖屏 H5 互动新闻。读者以“共享儿女”服务人员的身份，经历首次上门、建立信任、处理私人馈赠和面对服务人员更换。

## 本地预览

在项目目录运行：

```sh
python3 -m http.server 8000
```

然后打开 `http://localhost:8000/`。

如果需要通过 GitHub 网页快速发布，可以运行：

```sh
python3 tools/build_standalone.py
```

生成的 `upload/index.html` 已内嵌 CSS、JavaScript 和全部场景 SVG，可以单独上传到仓库根目录作为 `index.html`。

## 修改新闻正文链接

打开 `script.js`，将顶部的占位值替换为完整新闻 H5 的公开 URL：

```js
const GAME_CONFIG = Object.freeze({
  NEWS_URL: "https://example.com/your-news-article",
});
```

未替换时，读者点击“继续阅读现实案例”会看到配置提示，不会跳到错误地址。

## 替换场景图片

场景图片位于 `assets/images/`，均为可替换的编辑风格 SVG 占位图。正式素材建议保持 4:3.45 的竖版构图，并沿用现有文件名，这样不需要修改代码：

- `scene-home.svg`
- `scene-visit.svg`
- `scene-trust.svg`
- `scene-tea.svg`
- `scene-change.svg`
- `scene-ending.svg`

图片通过普通 `<img>` 元素显示，不会拉伸变形。替换为 JPG 或 WebP 时，需要同步修改 `script.js` 中 `art()` 调用里的扩展名。

## 分支状态

第 12 次服务中，如果王奶奶询问能否直接打电话：

| 电话选择 | 老人信任 | 家属信任 | 后续 |
| --- | ---: | ---: | --- |
| 可以直接打 | 5 | 0 | 进入送茶剧情 |
| 有事找女儿 | 3 | 4 | 跳过送茶，进入“关系需要边界” |

送茶剧情内部的分支为：

| 茶叶选择 | 老人信任 | 家属信任 | 服务状态 |
| --- | ---: | ---: | --- |
| 接受 | 5 | 0 | 终止 |
| 拒绝 | 3 | 4 | 继续 |
| 询问家属 | 4 | 4 | 继续 |

无论选择哪条分支，Scene 4 都会将新服务人员的老人信任度重置为 1 星。

## 测试

在项目目录运行状态机测试：

```sh
/System/Library/Frameworks/JavaScriptCore.framework/Versions/Current/Helpers/jsc tests/smoke.js
```

## 发布二维码

小游戏二维码应指向：

```text
https://haileycrimee-prog.github.io/shared-children/
```

项目中提供 `game-qr.svg` 和 `game-qr.png`，可直接放入新闻 H5。二维码内容一旦改变，需要重新运行生成脚本：

```sh
python3 -m pip install segno
python3 tools/generate_qr.py
```

`qr-embed-example.html` 是可供新闻 H5 参考的二维码嵌入样式。

## 技术说明

- 不使用 React、Vue、CDN、后端或第三方追踪。
- 不收集个人信息，不写 Cookie 或本地存储。
- 页面适配 320px–430px 手机宽度。
- 支持键盘焦点、屏幕阅读器状态播报和减少动态效果设置。
