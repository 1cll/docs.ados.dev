# Интеграция с Bitbucket

ADOS поддерживает Bitbucket Cloud.

## Настройка

### 1. Создание App Password

1. Перейдите в Bitbucket → Personal settings → **App passwords**
2. Нажмите **Create app password**
3. Выберите следующие разрешения:
   - `Repositories: Read, Write, Admin`
   - `Pull requests: Read, Write`
   - `Issues: Read, Write`
4. Сгенерируйте и скопируйте пароль

### 2. Регистрация в ADOS

1. Перейдите в **Settings** → **Connections** в панели управления
2. Нажмите **Add New Connection** → Выберите **Bitbucket**
3. Введите:
   - **Connection Name**: Любое имя (напр., `bitbucket-main`)
   - **Username**: Ваше имя пользователя Bitbucket
   - **App Password**: Сгенерированный пароль
4. Нажмите **Save**

### 3. Регистрация репозитория

```yaml
repos:
  - name: my-bb-repo
    owner: my-workspace
    repo: my-repo
    vcs_provider: bitbucket
    label: ados
    target_branch: main
```

## Bitbucket Server

Поддержка Bitbucket Server (on-premises) запланирована в будущих релизах.

> [!TIP]
> Workspace ID Bitbucket Cloud можно найти в Settings → Workspace settings.
