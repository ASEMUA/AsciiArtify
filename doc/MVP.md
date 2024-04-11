
# Minimum Viable Product (MVP):

`Ціль:` Створити мінімальний функціональний продукт, який може вивести на ринок та отримати зворотний зв'язок від користувачів. В нашому випадку це демонтрація роботи застосунку `AsciiArtify`

`Зміст документації:` Документація MVP повинна включати більше деталей порівняно із PoC. Зазвичай це включає визначення функціональних вимог, дизайн інтерфейсу, технічну архітектуру, план виконання, тестовий план, план масштабування та стратегію впровадження.

1. Перевіряємо роботу застосунку AsciiArtify
- Переадресуємо порти наступною командою:
```bash
@ASEMUA ➜ /workspaces/kbot (main) $ kubectl port-forward svc/argocd-server -n argocd 8090:443&
[1] 26375
@ASEMUA ➜ /workspaces/kbot (main) $ Forwarding from 127.0.0.1:8090 -> 8080
Forwarding from [::1]:8090 -> 8080
Handling connection for 8090
Handling connection for 8090
```
- Зробимо запит на вказаний порт та отримаємо відповідь у вигляді версії додатку:  
```bash
@ASEMUA ➜ /workspaces/kbot (main) $ curl localhost:8090
<a href="https://localhost:8090/">Temporary Redirect</a>.      
```
- Наче не все добре, але  ArgoCD з цим не згоден

![ambassador](1.PNG)  

- Перевіримо зміни:
```bash
@ASEMUA ➜ /workspaces/kbot (main) $ kubectl get svc -n demo
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                                 AGE
ambassador-admin   ClusterIP   10.43.236.112   <none>        8877/TCP                                                59m
demo-nats          ClusterIP   None            <none>        4222/TCP,6222/TCP,8222/TCP,7777/TCP,7422/TCP,7522/TCP   59m
cache              ClusterIP   10.43.121.15    <none>        6379/TCP                                                59m
db                 ClusterIP   10.43.123.254   <none>        3306/TCP                                                59m
demo-data          ClusterIP   10.43.28.8      <none>        80/TCP                                                  59m
ambassador         NodePort    10.43.129.77    <none>        80:30878/TCP                                            59m
demo-img           ClusterIP   10.43.65.76     <none>        80/TCP                                                  59m
demo-api           ClusterIP   10.43.144.197   <none>        80/TCP                                                  59m
demo-ascii         ClusterIP   10.43.144.223   <none>        80/TCP                                                  59m
demo-front         ClusterIP   10.43.128.24    <none>        80/TCP                                                  59m
```

3. Перевіримо роботу застосунка після виправлення помилки
- Для цього завантажимо файл що зберігається у нас в локальному сховищі на вітдалений сервер командою:
```bash
@ASEMUA ➜ /workspaces/kbot (main) $ wget -O /tmp/g.png https://img2.gratispng.com/20180406/xhq/kisspng-computer-icons-house-window-blinds-shades-brookl-adress-5ac7dd63724750.6622363615230477794681.jpg
--2024-04-11 13:32:41--  https://img2.gratispng.com/20180406/xhq/kisspng-computer-icons-house-window-blinds-shades-brookl-adress-5ac7dd63724750.6622363615230477794681.jpg
Resolving img2.gratispng.com (img2.gratispng.com)... 172.67.188.241, 104.21.65.55, 2606:4700:3037::6815:4137, ...
Connecting to img2.gratispng.com (img2.gratispng.com)|172.67.188.241|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 50910 (50K) [image/jpeg]
Saving to: ‘/tmp/g.png’

/tmp/g.png                      100%[====================================================>]  49.72K  --.-KB/s    in 0.002s  

2024-04-11 13:32:41 (25.4 MB/s) - ‘/tmp/g.png’ saved [50910/50910]

@ASEMUA ➜ /workspaces/kbot (main) $ curl -F 'image=@g.png' localhost:8090/img/
curl: (26) Failed to open/read local data from file/application
```
- Отримаємо результат прямо в консолі:  

![Result](.img/argo_res.png)  

