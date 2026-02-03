`$PSVersionTable - показывает версию`
`measure - аналог wc -l`
`Get-ItemsChild - ls`
`Get-Conetent - Cat`

- **Найти все команды, связанные с «Service»:**
    
    PowerShell
    
    ```
    Get-Command *Service*
    ```
    
- **Найти все команды, которые что-то получают (глагол Get):**
    
    PowerShell
    
    ```
    Get-Command -Verb Get
    ```
    
- **Найти все команды для работы с процессами:**
    
    PowerShell
    
    ```
    Get-Command -Noun Process
    ```
    

---

### 2. Как понять, что делает команда? (`Get-Help`)

Как только ты нашел имя (например, `Get-Content`), тебе нужно понять, как им пользоваться. Это аналог `man` в Linux.

PowerShell

```
Get-Help Get-Content
```

_Если хочешь увидеть примеры использования (самое полезное):_

PowerShell

```
Get-Help Get-Content -Examples
```

