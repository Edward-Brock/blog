# Linux 提示 You have mail

## 问题描述

每次打开终端窗口，总是会显示 You have mail. 的提示，之前一直没有注意，这几次打开的时候才有发现。

出现这种情况的原因，是因为系统出现错误（例如 cron 出现权限问题等）需要邮件通知用户。系统会将检查的各种状态汇总，定期发送本机用户邮箱中。邮件阅读过后则不会再提示。

这个邮箱本质上是个文件，邮箱位置位于：`/var/mail/用户名` 里，可以使用 `cat` 命令查看其内容。

## 解决办法

删除该文件即可

`sudo rm /var/mail/用户名  
sudo touch /var/mail/用户名`

再次打开终端窗口，You have new mail. 的提示消失。

如果一直向你发送邮件，你又不想看到可以使用以下命令：

`echo "unset MAILCHECK">> /etc/profile && source /etc/profile && cat /dev/null > /var/spool/mail/root`