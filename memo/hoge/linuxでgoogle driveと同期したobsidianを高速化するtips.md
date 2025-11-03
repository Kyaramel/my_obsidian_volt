- linuxでgoogle driveを使用する（広義的に）には`rclone`を使用する
```
sudo apt update
sudo apt install rclone
```
- `rclone`の初期設定
	1. ターミナルで`rclone config`を実行
	2. `n`
	3. `name>`:任意のリモート名を入力（例: `ikako_gdrive`）
	4. `Storage>`: `gdrive`の番号を探して入力
	5. `client_id>`: デフォルトのままenter
	6. `client_secret>`: デフォルトのままenter
	7. `scope>`: `1`
	8. `root_folder_id>`: デフォルトのままenter
	9. `service_account_file>`: デフォルトのままenter
	10. `Edit advanced config?`: `n`
	11. `Use auto config>`: `y` googleアカウントの認証画面が出る
	12. ブラウザでgoogleアカウントを認証する
	13. `Configure this as a theam drive?`: `n`
	14. 設定内容の確認画面が表示されたら`y`
	15. `q`で終了