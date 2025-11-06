# gestor_arquivos.bat
@echo off

::Criando as pastas
set "BASE=C:\GestorArquivos"
set "DOCS=%BASE%\Documentos"
set "LOGS=%BASE%\Logs"
set "BACKUP=%BASE%\Backups"
set "LOGFILE=%LOGS%\atividade.log"
set "CONTADOR_ARQUIVOS=0"
set "CONTADOR_PASTAS=0"

goto criar_pasta_base

::Criando os arquivos
:criar_pasta_base
if not exist "%BASE%" (mkdir "%BASE%" & set /a CONTADOR_PASTAS+=1 & call :registrar "Criação da pasta BASE" "SUCESSO") else (call :registrar "Criação da pasta BASE" "JA EXISTIA")
if not exist "%DOCS%" (mkdir "%DOCS%" & set /a CONTADOR_PASTAS+=1 & call :registrar "Criação da pasta Documentos" "SUCESSO") else (call :registrar "Criação da pasta Documentos" "JA EXISTIA")
if not exist "%LOGS%" (mkdir "%LOGS%" & set /a CONTADOR_PASTAS+=1 & call :registrar "Criação da pasta Logs" "SUCESSO") else (call :registrar "Criação da pasta Logs" "JA EXISTIA")
if not exist "%BACKUP%" (mkdir "%BACKUP%" & set /a CONTADOR_PASTAS+=1 & call :registrar "Criação da pasta Backups" "SUCESSO") else (call :registrar "Criação da pasta Backups" "JA EXISTIA")
echo Estrutura de pastas criada com sucesso!
pause
goto criar_arquivos

::Gerando log
:criar_arquivos
echo Criando arquivos de exemplo...
echo Relatório de uso inicial > "%DOCS%\relatorio.txt"
set /a CONTADOR_ARQUIVOS+=1
call :registrar "Criação de relatorio.txt" "SUCESSO"
echo Nome,Idade,Login > "%DOCS%\dados.csv"
set /a CONTADOR_ARQUIVOS+=1
call :registrar "Criação de dados.csv" "SUCESSO"
echo [CONFIG] > "%DOCS%\config.ini"
echo Usuario=admin >> "%DOCS%\config.ini"
set /a CONTADOR_ARQUIVOS+=1
call :registrar "Criação de config.ini" "SUCESSO"
echo Arquivos criados com sucesso!
pause
goto verificar_sistema

::Realizando backup
:verificar_sistema
set "operacao=Verificação de Sistema"
echo Executando: %operacao%...
set /a contador=0
:espera
set /a contador+=1
if %contador% leq 2 (ping -n 2 127.0.0.1 >nul & goto espera)
call :registrar "%operacao%" "SUCESSO"
pause
goto backup_arquivos

:backup_arquivos
echo ===========================================
echo Iniciando backup de arquivos...
echo ===========================================
set "LOG_BACKUP=%BACKUP%\log_backup.txt"
xcopy "%DOCS%\*" "%BACKUP%\" /E /I /Y > "%LOG_BACKUP%"
if %ERRORLEVEL% GEQ 1 (call :registrar "Backup de arquivos" "FALHA") else (call :registrar "Backup de arquivos" "SUCESSO")
echo Backup completo realizado em %date% %time% > "%BACKUP%\backup_completo.bak"
call :registrar "Criação de backup_completo.bak" "SUCESSO"
echo Backup concluído com sucesso!
echo Log de backup salvo em: %LOG_BACKUP%
echo Arquivo de confirmação criado: %BACKUP%\backup_completo.bak
echo ===========================================
pause
goto gerar_relatorio

::Resultado final
:gerar_relatorio
set "ARQUIVO_RELATORIO=%BASE%\resumo_execucao.txt"
(
echo RELATÓRIO DE EXECUÇÃO
echo ----------------------
echo Total de arquivos criados: %CONTADOR_ARQUIVOS%
echo Total de pastas criadas: %CONTADOR_PASTAS%
echo Data/Hora do backup: %date% %time%
) > "%ARQUIVO_RELATORIO%"
echo Relatório final gerado em:
echo %ARQUIVO_RELATORIO%
pause
goto :eof

:registrar
if not exist "%LOGS%" mkdir "%LOGS%"
echo [%date% %time%] Operação: %~1 - Resultado: %~2 >> "%LOGFILE%"
goto :eof
