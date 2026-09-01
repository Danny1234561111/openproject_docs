---
sidebar_navigation:
  title: Документы
  priority: 900
description: Настройки модуля документов в OpenProject.
keywords: категория документа, категории документов, документы, совместная работа, категория, категории, совместная работа в реальном времени, редактирование документа
---
# Настройки модуля документов
На этой странице описаны доступные настройки для модуля **Документы** в администрировании OpenProject.
## Типы документов
> [!NOTE]
>
> До OpenProject 17.0 типы документов назывались _категориями_ и настраивались в разделе _Администрирование → Файлы → Категории_.

Чтобы создать или отредактировать категории документов в OpenProject, перейдите в _Администрирование → Документы_. Здесь вы автоматически увидите все существующие типы документов:

- Столбец **Тип** перечисляет все существующие названия типов документов
- Столбец **Документы** показывает количество документов этого конкретного типа

Вы можете настроить элементы в списке, используя опции за меню **Еще (три точки)** справа. Вы также можете изменить порядок, используя маркер перетаскивания слева.

![Обзор типов документов в администрировании OpenProject](openproject_system_guide_documents_types_overview.png)

### Создание нового типа документа
Чтобы создать новый тип документа, выберите кнопку **+ Добавить** в правом верхнем углу.

Затем вы можете назвать новый тип, активировать его. Вы можете дополнительно установить этот тип как значение **По умолчанию**.

> [!NOTE]
> Установка этого типа по умолчанию переопределит предыдущий приоритет по умолчанию.

Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

![Создание нового типа документа в OpenProject](openproject_system_guide_documents_types_new_form.png)

### Редактирование типа документа
Чтобы **отредактировать** существующий тип, либо нажмите на название напрямую, либо выберите опцию **Редактировать** из меню **Еще (три точки)** в правом конце строки.

![Редактирование типа документа в администрировании OpenProject](openproject_system_guide_documents_types_edit.png)

### Удаление типа документа
Чтобы удалить тип документа, откройте меню **Еще (три точки)** в правом конце строки и нажмите на значок **удаления**.

![Кнопка удаления типа документа в администрировании OpenProject](openproject_system_guide_documents_types_delete_button.png)

Вы увидите диалоговое окно, информирующее о последствиях.

- Если тип документа не используется, это не имеет значительных последствий.
  ![Предупреждающее сообщение при удалении неиспользуемого типа документа в OpenProject](openproject_system_guide_documents_types_delete_message_type_unused.png)
- Если тип документа используется, вам нужно будет выбрать другой тип для переназначения
  ![Предупреждающее сообщение при удалении используемого типа документа в OpenProject, запрашивающее переназначение документов на другой тип](openproject_system_guide_documents_types_delete_message_type_used.png)
- Если тип документа является последним существующим, вы не сможете его удалить. Всегда должен быть настроен как минимум один тип документа. В этом случае вы можете сначала создать другой тип документа.
  ![Предупреждающее сообщение о том, что удаление последнего существующего типа документа не разрешено в OpenProject](openproject_system_guide_documents_types_delete_message_type_last.png)

## Совместная работа в реальном времени в документах
Совместная работа в реальном времени для модуля **Документы** OpenProject была представлена с выпуском 17.0. Когда она включена, она позволяет нескольким пользователям одновременно редактировать один и тот же документ. Изменения синхронизируются мгновенно, и пользователи могут видеть курсоры и правки друг друга по мере их выполнения. Это улучшает совместную работу, особенно для команд, работающих над общей документацией или заметками встреч.

С технической точки зрения, совместная работа в реальном времени зависит от работающего [сервера Hocuspocus](https://github.com/opf/openproject/tree/dev/extensions/op-blocknote-hocuspocus), который обрабатывает синхронизацию между пользователями. OpenProject подключается к этой службе, чтобы обеспечить беспрерывный опыт совместного редактирования в документах.

![Настройки администрирования для совместной работы в документах в реальном времени в OpenProject](openproject_system_guide_documents_real_time_collaboration.png)

> [!IMPORTANT]
>
> Совместная работа в реальном времени доступна для следующих типов установки. Однако она может потребовать правильной настройки, прежде чем будет полностью включена:
>
> - Контейнерные установки
> - Облачные установки
>
> Установки в пакетах (DEB/RPM) требуют дополнительной ручной настройки. Это включает установку и настройку [сервера Hocuspocus](https://github.com/opf/openproject/tree/dev/extensions/op-blocknote-hocuspocus) для включения совместной работы в реальном времени.

### Включение совместной работы в реальном времени для установок в пакетах
#### 1. Установка hocuspocus
Самый простой способ установить hocuspocus — использовать контейнер Docker.
Вы можете сделать это, выполнив следующие шаги.
Создайте каталог hocuspocus:

```shell
mkdir hocuspocus
cd hocuspocus
```

Затем вы можете создать файл docker-compose.yml со следующим содержимым внутри каталога hocuspocus:

```yaml
services:
  hocuspocus:
    image: <hocuspocus_image>
    restart: unless-stopped
    environment:
      SECRET: "secret123"
    ports:
      - "127.0.0.1:1234:1234"
```

Replace the `<hocuspocus_image>` with the image from [here](https://github.com/opf/openproject-docker-compose/blob/stable/17/docker-compose.yml#L122).

Run hocuspocus:

```shell
docker compose up -d
```

#### 2. Configure Apache

> [!NOTE]
> This part of the docs assumes that you are using the generated Apache config by the OpenProject wizard

Create `/etc/openproject/addons/apache2/custom/vhost/hocuspocus.conf` with the following content:

```apache
ProxyPass        /hocuspocus  ws://127.0.0.1:1234/hocuspocus
ProxyPassReverse /hocuspocus  ws://127.0.0.1:1234/hocuspocus
```

**For Debian/Ubuntu-based systems, run the following commands:**

Enable the `proxy_wstunnel` module:

```shell
sudo a2enmod proxy_wstunnel
```

Restart Apache:

```shell
sudo service apache2 restart
```

**For RHEL/CentOS-based systems, run the following command:**

```shell
sudo  service httpd restart
```

#### 3. Enable real-time collaboration

Вручную настройте URL-адрес сервера и секретный ключ в настройках администрирования Документы в OpenProject.
Здесь вам нужно предоставить URL-адрес в следующем формате: wss://<ваш_хостнейм_op>/hocuspocus.
Если вы используете HTTP в вашем экземпляре, протокол должен быть ws:// вместо wss://.

> [!NOTE]
> The secret must be identical in both op-blocknote-hocuspocus and OpenProject.

![Administration settings for real-time documents collaboration in OpenProject](openproject_system_guide_documents_real_time_collaboration_settings.png)

For more background on this feature, see [this blog article](https://www.openproject.org/blog/real-time-collaboration-in-documents/) on the introduction of real-time collaboration in documents.
