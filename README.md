# adm_training_task17
<h1 align="center">Занятие 17. Основы сбора и хранения логов</h1>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">1. В Vagrant разворачиваем 2 виртуальные машины web и log</span></span></p>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">2. на web настраиваем nginx</span></span></p>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">3. на log настраиваем центральный лог сервер на любой системе на выбор</span></span></p>
<ul>
<li>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">journald;</span></span></p>
</li>
<li>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">rsyslog;</span></span></p>
</li>
<li>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">elk.</span></span></p>
</li>
</ul>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">4. настраиваем аудит, следящий за изменением конфигов nginx </span></span></p>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">Все критичные логи с web должны собираться и локально и удаленно.</span></span></p>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">Все логи с nginx должны уходить на удаленный сервер (локально только критичные).</span></span></p>
<p><span style="font-family: 'Roboto Light', serif;"><span style="font-size: small;">Логи аудита должны также уходить на удаленную систему.</span></span></p>
<h3 class="western"><a name="_heading=h.df570rpzx1qg"></a><span style="font-family: Roboto, serif;"><span style="font-size: small;">Используемые ОС</span></span></h3>
<p style="line-height: 108%; margin-bottom: 0.28cm;" align="justify"><span style="font-family: Roboto, serif;">Хостовая ОС Ubuntu 24.04 Desktop с установленным Vagrant 2.3.5. VirtualBox версия 7.0.16_Ubuntu r162802</span></span></p>
<h3 class="western"><span style="font-family: Roboto, serif;"><span style="font-size: small;">Выполнение</span></span></h3>
<p>******************************</p>
<p>Ввиду невозможности в нынешних реалиях воспользоваться стандартными репозиториями Vagrant (в РФ они сейчас не доступны), предложено обходное решение. Использован репозиторий, развернутый на&nbsp;<a href="https://vagrant.elab.pro/" rel="nofollow">https://vagrant.elab.pro/</a></p>
<p>Так как официальный пакет последней версии Vagrant также не доступен для скачивания, пакет взят оттуда же. Версия 2.3.5.</p>
<img width="411" height="88" alt="image" src="https://github.com/user-attachments/assets/7591c684-f543-4bf6-bfa8-3706a7648c97" />
<p>&nbsp;</p>
<p>Для подключения репозитория надо добавить в Vagrant-файл строку:</p>
<p><code>ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'</code></p>
<p>И изменить box_name и box_version (как в репозитории, если туда зайти).</p>
<img width="480" height="306" alt="image" src="https://github.com/user-attachments/assets/c34c2b46-a072-4edd-8ae6-6f0c8d26cde8" />
<p>******************************</p>
<p><span style="font-weight: 400;">Создадим каталог task17, а в нем Vagrantfile (взятый из методички и измененный в соответствии с нашим обходным решением), в котором будут указаны параметры наших ВМ. Измененный Vagrant-файл прикладываю сюда.</span></p>
<img width="616" height="666" alt="image" src="https://github.com/user-attachments/assets/412b92a7-d421-4324-941f-1ab463b2d03f" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">После создания Vagrantfile запустим команду <code>vagrant up</code>. Будет создано две ВМ <strong>web</strong> и <strong>log</strong>. Можно увидеть их в VirtualBox.</span></p>
<img width="1091" height="259" alt="image" src="https://github.com/user-attachments/assets/ae91f375-5a11-4484-af80-0a9b4f4910f5" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Заходим на web-сервер <code>vagrant ssh web</code> и переходим в root-пользователя <code>sudo -i</code></span></p>
<img width="789" height="478" alt="image" src="https://github.com/user-attachments/assets/e371f41e-6767-416f-b317-7102a8104a30" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Проверяем настройки даты и времени <code>timedatectl</code></span></p>
<img width="789" height="205" alt="image" src="https://github.com/user-attachments/assets/23e21c39-7bea-40ee-ac22-c66d4bbaf5f4" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Время и часовой пояс не совпадает с хостом. Нужно настроить синхронизацию времени. Проверим, какая утилита используется:</span></p>
<p><span style="font-weight: 300;"><code>systemctl status ntp</code></span></p>
<p><span style="font-weight: 300;"><code>systemctl status chronyd</code></span></p>
<p><span style="font-weight: 300;"><code>systemctl status systemd-timesyncd</code></span></p>
<img width="807" height="249" alt="image" src="https://github.com/user-attachments/assets/0c56a5c6-994d-4540-9f5e-0295d01709f2" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Есть сервис systemd-timesyncd. Включим его:</span></p>
<p><code>timedatectl set-ntp true</code></p>
<p><code>timedatectl set-timezone Europe/Moscow</code></p>
<p><code>systemctl start systemd-timesyncd</code></p>
<p><code>timedatectl</code></p>
<img width="807" height="249" alt="image" src="https://github.com/user-attachments/assets/1480503a-f1e2-4159-ae0e-fd415f25168f" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Аналогично включим синхронизацию времени на ВМ <strong>log</strong>.</span></p>
<img width="640" height="291" alt="image" src="https://github.com/user-attachments/assets/41da47e4-d7a8-4012-b41d-39163a488762" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">На ВМ <strong>web</strong> установим nginx:</span></p>
<p><code>apt update &amp;&amp; apt install -y nginx</code></p>
<p><span style="font-weight: 300;">Проверим, что nginx работает корректно:</span></p>
<p><code>systemctl status nginx</code></p>
<img width="810" height="426" alt="image" src="https://github.com/user-attachments/assets/d90f2142-813e-4456-9efe-d2610e9f65a2" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Заходим на ВМ <strong>log</strong> <code>vagrant ssh log</code> и переходим в root-пользователя <code>sudo -i</code></span></p>
<img width="810" height="489" alt="image" src="https://github.com/user-attachments/assets/50ab09e1-c3a3-4965-bad0-7d31e5fc8406" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Rsyslog должен быть установлен по умолчанию в нашей ОС, проверим это:</span></p>
<p><code>apt list rsyslog</code></p>
<img width="810" height="97" alt="image" src="https://github.com/user-attachments/assets/46c2f066-e467-4bb1-b71a-18b68b54aea8" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">Все настройки Rsyslog хранятся в файле /</span><em><span style="font-weight: 300;">etc</span></em><span style="font-weight: 300;">/rsyslog.conf&nbsp;</span></p>
<p><span style="font-weight: 300;">Для того, чтобы наш сервер мог принимать логи, нам необходимо внести следующие изменения в файл:&nbsp;</span></p>
<p><span style="font-weight: 300;">Раскомментируем строки для порта 514 (TCP и UDP)</span></p>
<img width="446" height="165" alt="image" src="https://github.com/user-attachments/assets/92025896-984c-4fc4-8ced-e25d3fe5e574" />
</span></p>
<p><span style="font-weight: 300;">В конец файла /etc/rsyslog.conf добавляем правила приёма сообщений от хостов:</span></p>
<p><code>#Add remote logs</code></p>
<p><code>$template RemoteLogs,"/var/log/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& ~</code></p>
<img width="698" height="125" alt="image" src="https://github.com/user-attachments/assets/5532530d-ede0-46d3-8026-d751a6060fbf" />
</span></p>
<p><span style="font-weight: 300;">Далее сохраняем файл и перезапускаем службу rsyslog: <code>systemctl restart rsyslog</code></p>
<p><span style="font-weight: 300;">Если ошибок не допущено, то у нас будут видны открытые порты TCP,UDP 514:</p>
<img width="1271" height="269" alt="image" src="https://github.com/user-attachments/assets/940059e6-48d5-4132-8c8c-7c460d226ed6" />
</span></p>
<p><span style="font-weight: 300;">Далее настроим отправку логов с web-сервера</span></p>
<p><span style="font-weight: 300;">Заходим на web-сервер и переходим в root-пользователя</span></p>
<p><span style="font-weight: 300;">Проверим версию nginx: <code>nginx -v</code></p>
<img width="392" height="70" alt="image" src="https://github.com/user-attachments/assets/96583ff5-8614-4b71-a9d0-19f4656918be" />
</span></p>
<p><span style="font-weight: 300;">Rsyslog также должен быть установлен в этой ОС. Если нет, установим и включим.</span></p>
<p><code>apt install rsyslog</code></p>
<p><code>systemctl enable rsyslog</code></p>
<p><code>systemctl start rsyslog</code></p>
<p><span style="font-weight: 300;">Находим в файле /etc/nginx/nginx.conf раздел с логами и приводим их к следующему виду:</span></p>
<p>error_log /var/log/nginx/error.log;</p>
<p>error_log syslog:server=192.168.56.15:514,tag=nginx_error;</p>
<p>access_log syslog:server=192.168.56.15:514,tag=nginx_access,severity=info combined;</p>
<p><span style="font-weight: 300;">Далее проверяем, что конфигурация nginx указана правильно: <code>nginx -t</code></p>
<img width="698" height="111" alt="image" src="https://github.com/user-attachments/assets/9b8b99a8-4d5e-4bf4-9dbf-4123f6ff4398" />
</span></p>
<p><span style="font-weight: 300;">Перезапускаем nginx: <code>systemctl restart nginx</code></p>
<p><span style="font-weight: 300;">Попробуем несколько раз зайти по адресу </span><span style="font-weight: 300;"><a href="http://192.168.56.10">http://192.168.56.10</a></span></p>
<p><span style="font-weight: 300;">Далее заходим на log-сервер и смотрим информацию об nginx:</span></p>
<p><code>cat /var/log/rsyslog/web/nginx_access.log</code></p>
<p><code>cat /var/log/rsyslog/web/nginx_error.log</code></p>
<img width="1310" height="364" alt="image" src="https://github.com/user-attachments/assets/759eedbd-e4cf-45b1-9755-7a7400e98598" />
</span></p>
<p><span style="font-weight: 300;">Лог доступа, как видим, есть. Лога ошибок нет.</span></p>
<p>Чтобы сгенерировать ошибку, можно переместить файл веб-страницы, который открывает nginx -</p>
<p><code>mv /var/www/html/index.nginx-debian.html /var/www/</code></p>
<p><code>systemctl restart nginx</code></p>
<img width="682" height="77" alt="image" src="https://github.com/user-attachments/assets/84d4d1aa-e45f-4654-951b-d7b727e8b432" />
</span></p>
<p><span style="font-weight: 300;">Снова попробуем несколько раз зайти по адресу </span><span style="font-weight: 300;"><a href="http://192.168.56.10">http://192.168.56.10</a></span></p>
<p>В браузере покажет 403 Forbidden. Смотрим логи еще раз:</p>
<p><code>cat /var/log/rsyslog/web/nginx_access.log</code></p>
<p><code>cat /var/log/rsyslog/web/nginx_error.log</code></p>
<img width="1840" height="994" alt="image" src="https://github.com/user-attachments/assets/fb013811-d066-47ec-839e-05cc7fdbc367" />
</span></p>
<p><span style="font-weight: 300;">Теперь есть и лог ошибок.</span></p>
<p><span style="font-weight: 300;">Аналогично в каталоге task17_1 с помощью Vagrant создадим третью ВМ с именем <strong>new</strong>. Не знаю, надо ли прикреплять сюда этот Vagrant-файл - он идентичен предыдущему, только там одна ВМ с именем <strong>new</strong> и IP <strong>192.168.56.20</strong></span></p>
<img width="1096" height="338" alt="image" src="https://github.com/user-attachments/assets/cc18d3da-0de0-4e4f-b541-e4308c7e1cb2" />
</span></p>
<p><span style="font-weight: 300;">Зайдем на нее по ssh и перейдем в root-пользователя. Rsyslog уже установлен и запущен. </p>
<img width="802" height="482" alt="image" src="https://github.com/user-attachments/assets/6dca92c2-0fcc-4984-bbd0-d4afcf535897" />
</span></p>
<p>Нужно настроить отправку всех логов на сервер. Создаем конфигурационный файл для rsyslog:</p>
<p><code>nano /etc/rsyslog.d/all.conf</code></p>
<p>Добавляем:</p>
<p><code>*.* @@192.168.56.15:514</code></p>
<p><em>* где <strong>192.168.56.15</strong>&nbsp;&mdash;&nbsp; IP-адрес нашего сервера логов,&nbsp;<strong>*.*</strong>&nbsp;&mdash; перенаправлять любой лог.</em></p>
<p>Перезапускаем rsyslog:</p>
<p><code>systemctl restart rsyslog</code></p>
<p>Заходим по ssh на ВМ <strong>log</strong> и видим, что в каталоге /var/log/rsyslog/ появился каталог new:</p>
<img width="514" height="184" alt="image" src="https://github.com/user-attachments/assets/503abbf3-5e1c-4085-ad93-9db5db040b07" />
</span></p>
<p>Смотрим его содержимое:</p>
<img width="583" height="205" alt="image" src="https://github.com/user-attachments/assets/57c2c93c-d550-4058-b006-b39abf9c3c13" />
</span></p>
<p>Видим там четыре лог-файла с машины <strong>new</strong>. Просмотрим systemd.log:</p>
<img width="809" height="397" alt="image" src="https://github.com/user-attachments/assets/818bfff8-425a-4018-8620-8c3e49f9f09e" />
</span></p>
<p>Забыли отсинхронизировать время на машине <strong>new</strong>. Делаем это и снова смотрим:</p>
<img width="809" height="397" alt="image" src="https://github.com/user-attachments/assets/a9d73f51-3639-4290-9964-d6b47226d78f" />
</span></p>
<p>Теперь правильное время. Задание завершено.</p>
