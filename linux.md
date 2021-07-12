

ps aux | grep -v grep | grep uwsgi | awk '{system("kill -9 "$2)}'