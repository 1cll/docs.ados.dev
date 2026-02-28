# Интеграция с GitLab

ADOS поддерживает GitLab.com и GitLab Self-Managed.

## Настройка

### 1. Создание Personal Access Token

1. Перейдите в GitLab → User Settings → **Access Tokens**
2. Нажмите **Add new token**
3. Выберите следующие области:
   - `api` — Полный доступ к API
   - `read_repository` — Чтение репозитория
   - `write_repository` — Запись в репозиторий
4. Сгенерируйте и скопируйте токен

### 2. Регистрация в ADOS

1. Перейдите в **Settings** → **Connections** в панели управления
2. Нажмите **Add New Connection** → Выберите **GitLab**
3. Введите:
   - **Connection Name**: Любое имя (напр., `gitlab-production`)
   - **Token**: Сгенерированный PAT
   - **Base URL**: `https://gitlab.com` (или ваш URL для Self-Managed)
4. Нажмите **Save**

### 3. Регистрация репозитория

```yaml
repos:
  - name: my-gitlab-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    label: ados
    target_branch: main
```

## GitLab Self-Managed

On-premises GitLab также поддерживается:

```yaml
repos:
  - name: my-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    vcs_base_url: https://gitlab.my-company.com
```

## Поддерживаемые события

| Событие | Описание |
|---------|----------|
| Issue Created | Автоматическое обнаружение задач с меткой |
| Merge Request | Отслеживание статуса MR, созданных ИИ |
| Pipeline | Мониторинг и авторемонт CI/CD |

> [!TIP]
> GitLab использует «Merge Request» вместо «Pull Request». ADOS предоставляет одинаковую функциональность вне зависимости от терминологии.
