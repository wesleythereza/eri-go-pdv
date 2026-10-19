# Material complementar — PDV móvel offline para a agricultura familiar (ERI-GO 2026, resumo expandido)

Pacote anônimo para avaliação às cegas. O pacote Java do aplicativo foi renomeado para `com.example.meucaixa`
e foram removidos arquivos de IDE, scripts auxiliares e o pacote de distribuição; nenhuma linha de lógica foi alterada.

## Conteúdo
- `app_android/`: código-fonte do aplicativo avaliado (Java 17, Android SDK 34, MVVM, Room 2.6.1).
- `verificacao/stubs/`: substitutos mínimos de Android, Room e LiveData, usados apenas para compilar e executar as classes reais fora do Android.
- `verificacao/src/Harness.java`: executa `SaleService` e `NewSaleViewModel` reais com DAOs em memória, rollback de transação e injeção de falha de E/S (casos E1–E6).
- `verificacao/src/sql_tests.py`: extrai as strings `@Query` reais de `ProductDao` e `ReportsDao` e as executa em SQLite com esquema derivado das entidades (casos S1–S3).
- `verificacao/resultados/`: saídas JSON das execuções.

## Reprodução (JDK 17+ e Python 3)
```bash
J=app_android/app/src/main/java/com/example/meucaixa
cd verificacao && mkdir -p out
javac -d out $(find stubs -name '*.java') ../$J/models/*.java ../$J/database/AppDatabase.java ../$J/database/*Dao.java \
  ../$J/services/SaleService.java ../$J/utils/SessionManager.java ../$J/ui/sales/NewSaleViewModel.java src/Harness.java
java -cp out Harness resultados/resultados_execucao.json
python3 -I src/sql_tests.py ../$J resultados/resultados_sql.json
```

## Limites
Os stubs não reproduzem o Room real (SQLite no dispositivo), a interface gráfica não é executada e o caso E5 reproduz
o caminho da interface chamando os mesmos métodos que `NewSaleFragment` e `CartAdapter` chamam.
