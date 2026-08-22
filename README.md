# remote-vault
The online back-up for polite puppy vault

the changes should apper online

graph TD

    %% Sekcja źródeł danych
    subgraph Zrodla [Źródła Danych]
        A1[(Baza SQL <br> Zamówienia)]
        A2(API <br> Ruch na stronie)
    end

    %% Sekcja pobierania i zapisu surowego
    subgraph Ingestion [Ekstrakcja i Surowe Dane]
        B{Apache Airflow}
        C[(Data Lake <br> Amazon S3)]
    end

    %% Sekcja transformacji i docelowego składowania
    subgraph Przetwarzanie [Transformacja i Hurtownia]
        D[Apache Spark <br> Czyszczenie danych]
        E[(Hurtownia Danych <br> Snowflake)]
    end

    %% Sekcja analityki
    subgraph Analityka [Odbiorcy Biznesowi]
        F[[Tableau <br> Dashboardy BI]]
    end

    %% Definicja połączeń (strzałek) pomiędzy węzłami
    A1 -->|Codzienny zrzut| B
    A2 -->|Strumień JSON| B
    B -->|Zapis surowy| C
    C -->|Odczyt| D
    D -->|Zapis ustrukturyzowany| E
    E -->|Zapytania SQL| F