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
<p><span style="font-weight: 400;">Создадим каталог task17, а в нем Vagrantfile (взятый из методички и измененный в соответствии с нашим обходным решением), в котором будут указаны параметры нашей ВМ. Измененный Vagrant-файл прикладываю сюда.</span></p>
<img width="616" height="666" alt="image" src="https://github.com/user-attachments/assets/412b92a7-d421-4324-941f-1ab463b2d03f" />
<p>&nbsp;</p>
<p><span style="font-weight: 300;">После создания Vagrantfile запустим нашу ВМ командой <code>vagrant up</code>. Будет создано две виртуальных машины <strong>web</strong> и <strong>log</strong>.</span></p>




