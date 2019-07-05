# create-shh-and-push-without-password

cd ~/.ssh

$ ls -al ~/.ssh
Спискок файлов в директории .ssh, если они есть
id_rsa.pub # Нам интересны эти два брата, точнее этот публичный код (паб)
id_rsa # Это для сверки с пабом


Если их нет, то будем генерить, после чего ласково попросят пароль, он должен быть сложным, в конечном итоге вводится он будет только один раз при старте сессии.
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
Enter file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
Enter passphrase (empty for no passphrase):
Enter same passphrase again:


Кстати, пароль можно и поменять:
$ ssh-keygen -p # Введете старый, а потом новый.

Консоль выведет следующее:
Your identification has been saved in /Users/you/.ssh/id_rsa.
Your public key has been saved in /Users/you/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com


Теперь надо добавить ваши ключи в ssh-агент
Удостоверьтесь в том, что он у вас есть — запустите
$ ssh-agent -s

Должен быть следующий однострочный (!) вывод, номер pid будет другим, все остальное от лукавого:
Agent pid 59566

Если нет, то скорее всего в терминале другая оболочка, тогда:
$ eval $(ssh-agent -s)
Agent pid 59566


Вот теперь можно добавить ключи в ssh-агент
$ ssh-add ~/.ssh/id_rsa

для github:
$ ssh -T git@github.com

В каждом случае, если все пройдет успешно, то вы получите следующие сообщения:
Hi #{username}! You've successfully authenticated, but GitHub does not provide shell access.


Если вы не пользовались SSH, то ваши уже существующие локальные репо будут работать по http/https, поэтому надо сменить адреса. Адреса удаленных репозиториев проверяются командой $ git remote -v. Для смены адресов git есть специальная команда: $ set-url, ввести следующее:
$ git remote set-url origin git@github.com:USERNAME/OTHERREPOSITORY.git


нужно ещё автоматически добавлять этот ключ в агент, сделать это можно так
Add this command to your ~/.bashrc file, then logout and back in to take effect:

if [ ! -S ~/.ssh/ssh_auth_sock ]; then
eval ssh-agent
ln -sf "$SSH_AUTH_SOCK" ~/.ssh/ssh_auth_sock
fi
export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
ssh-add -l > /dev/null || ssh-add
