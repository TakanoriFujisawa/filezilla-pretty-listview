# FileZilla の外観変更

## Filezilla View のアイコン描画について (wxGTK)

src/interface/systemimagelist.cpp に記述あり

1. `CSystemImageList::CreateSystemImageList` にて以下のリスト m_pImageList を作成

* 0 ファイル, ART_FILE
* 1 閉じたフォルダ
* 2 フォルダ
* 3 ファイル (シンボリックリンク)
* 4 閉じたフォルダ (シンボリックリンク)
* 5 フォルダ (シンボリックリンク)

2. このリストは `wxTreeCtrl::SetImageList` にてツリービューに登録され，
表示サイズに応じて切り替えられる

3. `wxArtProvider::GetBitmap` にてリソース中の file.png, folderclosed.png, folder.png 読み込み

4. `CSystemImageList::GetIconIndex` にてファイル名からアイコンインデックス対応 (0-5に)

5. 拡張子をもつファイルは wxMimeTypesManager (インスタンス wxTheMimeTypesManager) から
アイコンのエントリを検索する． ただ， 起動時点ではここにはなんのエントリも入っていない様子


## 改変内容

1. locale_initializer.cpp の main 直後に以下を追加

```c
wxSetEnv("GTK2_RC_FILES", "/usr/share/themes/Ambiance/gtk-2.0/gtkrc");
```

2. FileZilla.cpp の `CFileZillaApp::OnInit` の CMainFrame 作成直前に
wxMimeTypesManager のエントリを追加するコードを挿入

3. resources/mime フォルダ作成，いろいろな拡張子の対応アイコンを設置

4. resources/humanity フォルダ作成，Ubuntu の UI に合ったアイコン

5. fzdefaults.xml ファイルを作成，以下の設定を適用
/usr/local/share/filezilla/fzdefaults.xml に設置

```xml
<Setting name="Theme">humanity/</Setting>
<!-- 拡張子の説明が入らずあまり意味がないので非表示に -->
<Setting name="Local filelist shown columns">1101</Setting>
<Setting name="Local filelist colwidths">273 99 100 120</Setting>

<Setting name="Greeting version">3.27.1</Setting>
<Setting name="Config Location"></Setting>
<Setting name="Kiosk mode">0</Setting>
<Setting name="Disable update check">1</Setting>
<Setting name="Show notification bubbles">1</Setting>
```

## その他

Windows は SHFILEINFO がそのまま (WXHIMAGELIST) リストになる模様

