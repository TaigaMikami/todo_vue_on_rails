# TODO vue on rails

## How to make environment


| フレームワーク・ツール | バージョン |
----|---- 
| Ruby | 2.5.0 |
| RoR | 5.1.5 |
| node | 8.9.1 |
| yarn | 1.3.2 |
| webpacker | 3.5 |
| Vue.js | 2.9.3 |

```
rails new todo --webpack=vue
```

### foremanの設定
Gemfileにforemanインストール

```
gem 'foreman'
```

 以下のファイルを作成する

bin/server 
```
#!/bin/bash -i
bundle install
bundle exec foreman start -f Procfile.dev
```

Procfile
```
web: bundle exec rails s
# watcher: ./bin/webpack-watcher
webpacker: ./bin/webpack-dev-server
```

bin/serverのパーミッションを変更する
```
chmod 777 bin/server
```

以下を実行して `http://localhost:5000` にアクセス
```
bin/server
```