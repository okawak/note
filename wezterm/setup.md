# WezTermの設定

私のターミナル遍歴としては、[iTerm2](https://iterm2.com/)、[VSCode](https://code.visualstudio.com/)のターミナル、
[Warp](https://www.warp.dev/)というように使ってきた。

よりモダンで優れたターミナルはないかと探していたところ、[WezTerm](https://wezfurlong.org/wezterm/index.html)というものを知り、
これを使うことにした。

## フォントのインストール

Warpを使用していた際に少し不便だと感じたところは、日本語入力を受け付けてもらえないところで、
日本語入力のままキーボードを打つと何も表示されない部分が不便に思っていた。(エンターで表示されるようになるが)

また、Warpの大きな特徴であるAI機能をなかなか使いこなせていなかった面もあり、新たなターミナルを使うことにした。
日本語入力をすることもあるということで、適切なfontを探すと

- **Firge35 Console**(<https://github.com/yuru7/Firge>)

というフォントが良さそうに感じた。
まずは、ここからreleaseページに行き、ダウンロードしフォントをインストールした。
(brewではインストールできない？)

## WezTermのインストール

brewでインストールできる。

```console
brew install wezterm
```

caskオプションはなくても自動で判断してくれるようになったのでGUIアプリケーションかどうかを気にせず、そのままbrew installする。

## 設定ファイル

WezTermで一番便利だと思った点は、設定ファイルはluaで全て記述できる(GUI上でぽちぽち押さずに、ファイルさえ共有すれば同じ設定になる)点で、
neovimの設定もluaで書けることから学習コストがそんなに高くないなと思ったのが魅力の一つである。

XDGの思想に基づいて、設定ファイルは、`${HOME}/.config/wezterm/wezterm.lua`に書く。
このファイルの最小構成としては、

```lua
local wezterm = require("wezterm")

local config = {}

if wezterm.config_builder then
  config = wezterm.config_builder()
end

return config
```

のような形で、これでデフォルトの設定が読み込まれることになる。
以降は細かな個々の設定について述べる。

### 画面の設定

ターミナルを起動するときは大抵大画面で用いたい。
起動させたときにデフォルトでフルスクリーン表示するための設定は次のように書ける。
また、背景が少しだけ透けるのが好きなので、opacityを0.9に設定している。

```lua
config.window_background_opacity = 0.90
-- startup with full-screen mode
local mux = wezterm.mux
wezterm.on("gui-startup", function(cmd)
    local tab, pane, window = mux.spawn_window(cmd or {})
    window:gui_window():toggle_fullscreen()
end)
```

またkeybindsの設定で詳細は述べるが、フルスクリーンとその解除のキー設定をALT(Option) + Enterに設定しておく。

```lua
-- snip
{ key = "Enter", mods = "ALT", action = act.ToggleFullScreen },
-- snip
```

### フォント、カラースキームの設定

フォントはすでに紹介した**Firge**を用いる。
インストールがうまくいっていなかった場合、fontに関するエラーログが出てくるので、要確認。

フォントサイズは少し大き目が好きなので、(小さい文字の方が多くの情報を見れるが、目が疲れてしまう)このような設定にした。

```lua
-- colors
config.color_scheme = "Tokyo Night Storm"

-- font
config.font = require("wezterm").font("Firge35Nerd Console")
config.font_size = 15.0
config.window_frame = {
  font = wezterm.font({ family = "Firge35Nerd Console", weight = "Bold" }),
  font_size = 13.0,
}
```

カラースキームはnvimでもお世話になっているTokyo Night Stormを使用することにする。
なんとなく個人的に好きな色です。

### その他の設定

設定を行うごとにこれから記述します。
