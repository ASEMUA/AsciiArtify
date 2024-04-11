
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

2. Виравлення проблеми з конфігураційними файлами
- після попередніх експериментів перевіримо мережеві налаштування нашого застосунку:
```bash
✗ k get svc -n demo
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)               AGE
ambassador         LoadBalancer   10.43.190.212   <pending>     80:30092/TCP          84s
```
- заглянемо в інтерфейс ArgoCD, де відразу помітимо що стан здоров'я нашого додатку `ambassador` невизначено, отже копаємо сюди.

![ambassador](1.png)  

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
curl -F 'image=@g.png' localhost:8081/img/
```
- Отримаємо результат прямо в консолі:  

![Result](.img/argo_res.png)  

4. Зафіксуємо весь описаний процес в наглядному форматі на відео:

[![MPV Demo](.img/argo_mvp.png)](https://www.youtube.com/watch?v=uGHwzDpGGE0)
