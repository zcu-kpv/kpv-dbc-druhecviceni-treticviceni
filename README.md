# WPF Aplikace s Entity Framework Core - Kompletní didaktický návod

## Obsah
1. [Úvod do projektu](#kapitola-1-úvod-do-projektu)
2. [Příprava prostředí a vytvoření projektu](#kapitola-2-příprava-prostředí-a-vytvoření-projektu)
3. [Instalace NuGet balíčků](#kapitola-3-instalace-nuget-balíčků)
4. [Vytvoření struktury dat - Student entita](#kapitola-4-vytvoření-struktury-dat---student-entita)
5. [Databázový kontext - StudentContext](#kapitola-5-databázový-kontext---studentcontext)
6. [Seedování dat](#kapitola-6-seedování-dat)
7. [Databázové migrace - Teorie a praxe](#kapitola-7-databázové-migrace---teorie-a-praxe)
8. [XAML a MainWindow - UI struktura](#kapitola-8-xaml-a-mainwindow---ui-struktura)
9. [Code-behind - MainWindow.xaml.cs](#kapitola-9-code-behind---mainwindowxamlcs)
10. [Spuštění a testování aplikace](#kapitola-10-spuštění-a-testování-aplikace)
11. [Pokročilé koncepty a best practices](#kapitola-11-pokročilé-koncepty-a-best-practices)
12. [Přílohy a užitečné odkazy](#přílohy)

---

## Kapitola 1: Úvod do projektu

### 1.1 Co je WPF?

**WPF (Windows Presentation Foundation)** je moderní framework od Microsoftu pro tvorbu desktopových aplikací pro Windows. WPF používá:
- **XAML** (eXtensible Application Markup Language) pro definici uživatelského rozhraní
- **C#** pro business logiku (code-behind)
- **Data binding** pro propojení UI a dat
- **MVVM pattern** (Model-View-ViewModel) - architektonický vzor, který budeme používat v pokročilejších cvičeních

### 1.2 Architektura tohoto projektu

Tento projekt demonstruje **základní WPF aplikaci** s připojením k databázi pomocí **Entity Framework Core**. Jedná se o **Code-First přístup**, kde:

1. **Model** - Definujeme C# třídy (`Student.cs`)
2. **Context** - Propojení s databází (`StudentContext.cs`)
3. **View** - XAML soubory (`MainWindow.xaml`)
4. **Code-behind** - Logika UI (`MainWindow.xaml.cs`)

```
WPF_Aplikace_s_db/
├── Data/
│   ├── Student.cs          # Entitní třída (tabulka v DB)
│   └── StudentContext.cs   # DbContext (propojení s DB)
├── MainWindow.xaml         # Definice UI
├── MainWindow.xaml.cs      # Logika UI
├── App.xaml                # Konfigurace aplikace
├── App.xaml.cs             # Entry point aplikace
└── WPF_Aplikace_s_db.csproj # Projekt soubor
```

### 1.3 Co se naučíte

- ✅ Vytvořit WPF projekt v Visual Studio
- ✅ Pracovat s Entity Framework Core
- ✅ Používat SQL Server LocalDB
- ✅ Definovat databázové entity s atributy (validace)
- ✅ Seedovat data do databáze
- ✅ Vytvářet XAML rozhraní s DataGrid
- ✅ Implementovat CRUD operace (Create, Read, Update, Delete)
- ✅ Používat data binding
- ✅ Pracovat s databázovými migraci

### 📋 Shrnutí kapitoly 1
- WPF je framework pro desktopové aplikace ve Windows
- Používá XAML pro UI a C# pro logiku
- Projekt používá Entity Framework Core Code-First přístup
- LocalDB umožňuje lokální vývoj bez instalace plného SQL Serveru

### 💡 Cheat Sheet - Základní pojmy
| Pojem | Význam |
|-------|---------|
| **WPF** | Windows Presentation Foundation - UI framework |
| **XAML** | Značkovací jazyk pro definici UI |
| **EF Core** | Entity Framework Core - ORM pro práci s DB |
| **Code-First** | Vytváření DB ze C# tříd |
| **LocalDB** | Lokální instance SQL Serveru |
| **MVVM** | Model-View-ViewModel architektura (použijeme později) |

---

## Kapitola 2: Příprava prostředí a vytvoření projektu

### 2.1 Požadavky

**Před začátkem se ujistěte, že máte nainstalováno:**

1. **Visual Studio 2022** (Community, Professional nebo Enterprise)
   - S workloadem ".NET desktop development"
2. **.NET 10 SDK** (nebo novější)
   - Stáhněte z: https://dotnet.microsoft.com/download
3. **SQL Server LocalDB** 
   - Součástí Visual Studio workloadu "Data storage and processing"

### 2.2 Vytvoření nového WPF projektu

#### Krok 1: Otevřete Visual Studio 2022

1. Spusťte Visual Studio 2022
2. Klikněte na **"Create a new project"**

#### Krok 2: Vyberte šablonu projektu

1. V search boxu zadejte: `WPF Application`
2. Vyberte **"WPF Application"** (C#)
3. Klikněte **Next**

#### Krok 3: Konfigurace projektu

Zadejte následující informace:

- **Project name:** `WPF_Aplikace_s_db`
- **Location:** Vyberte složku, kde chcete projekt uložit
- **Solution name:** `WPF_Aplikace_s_db`
- Zaškrtněte **"Place solution and project in the same directory"**

Klikněte **Next**

#### Krok 4: Framework selection

- **Framework:** Vyberte **.NET 10.0** (nebo nejnovější dostupnou verzi)
- Klikněte **Create**

### 2.3 Struktura vytvořeného projektu

Visual Studio automaticky vytvoří:

```
WPF_Aplikace_s_db/
├── App.xaml              # Konfigurace aplikace
├── App.xaml.cs           # Code-behind pro App.xaml
├── AssemblyInfo.cs       # Metadata sestavení
├── MainWindow.xaml       # Hlavní okno aplikace
├── MainWindow.xaml.cs    # Code-behind pro MainWindow
└── WPF_Aplikace_s_db.csproj # Projektový soubor
```

### 2.4 Porozumění základním souborům

#### **App.xaml** - Vstupní bod aplikace

```xml
<Application x:Class="WPF_Aplikace_s_db.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
         
    </Application.Resources>
</Application>
```

**Vysvětlení atributů:**
- `x:Class` - Namespace a název třídy v C#
- `xmlns` - XML namespace pro WPF elementy
- `xmlns:x` - Namespace pro XAML direktivy (např. x:Name, x:Class)
- `StartupUri` - Určuje, které okno se spustí při startu aplikace

#### **App.xaml.cs** - Code-behind

```csharp
namespace WPF_Aplikace_s_db
{
    public partial class App : Application
    {
    }
}
```

### 📋 Shrnutí kapitoly 2
- Vytvořili jsme nový WPF projekt v Visual Studio 2022
- Projekt cílí na .NET 10
- Visual Studio automaticky vytvořilo základní strukturu
- App.xaml definuje vstupní bod aplikace
- MainWindow.xaml je hlavní okno

### 💡 Cheat Sheet - Visual Studio shortcuts
| Shortcut | Akce |
|----------|------|
| **Ctrl + Shift + B** | Build solution |
| **F5** | Spustit s debuggerem |
| **Ctrl + F5** | Spustit bez debuggeru |
| **Ctrl + Shift + N** | Nový projekt |
| **Ctrl + .** | Quick Actions (opravy kódu) |

---

## Kapitola 3: Instalace NuGet balíčků

### 3.1 Co jsou NuGet balíčky?

**NuGet** je správce balíčků pro .NET. Umožňuje instalovat knihovny třetích stran do vašeho projektu.

### 3.2 Jaké balíčky budeme potřebovat?

Pro tento projekt potřebujeme **3 NuGet balíčky**:

| Balíček | Verze | Účel |
|---------|-------|------|
| **Microsoft.EntityFrameworkCore.SqlServer** | 10.x (nejnovější) | SQL Server provider pro EF Core |
| **Microsoft.EntityFrameworkCore.Tools** | 10.x (nejnovější) | Nástroje pro migrace (PMC) |
| **PropertyChanged.Fody** | 4.x (nejnovější) | Automatická implementace INotifyPropertyChanged |

### 3.3 Instalace NuGet balíčků

#### Metoda 1: Pomocí NuGet Package Manager (GUI)

1. **Pravý klik na projekt** v Solution Exploreru
2. Vyberte **"Manage NuGet Packages..."**
3. Klikněte na tab **"Browse"**

##### Instalace Microsoft.EntityFrameworkCore.SqlServer

1. Do search boxu zadejte: `Microsoft.EntityFrameworkCore.SqlServer`
2. Vyberte balíček od **Microsoft**
3. Na pravé straně vyberte **nejnovější stabilní verzi** (10.x)
4. Klikněte **Install**
5. Přijměte licenční podmínky

##### Instalace Microsoft.EntityFrameworkCore.Tools

1. Do search boxu zadejte: `Microsoft.EntityFrameworkCore.Tools`
2. Vyberte balíček od **Microsoft**
3. Vyberte **nejnovější stabilní verzi** (10.x)
4. Klikněte **Install**

##### Instalace PropertyChanged.Fody

1. Do search boxu zadejte: `PropertyChanged.Fody`
2. Vyberte balíček od **Fody**
3. Vyberte **nejnovější stabilní verzi** (4.x)
4. Klikněte **Install**

#### Metoda 2: Pomocí Package Manager Console (doporučeno)

1. Otevřete **Package Manager Console**:
   - Menu: **Tools → NuGet Package Manager → Package Manager Console**

2. Spusťte následující příkazy:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package PropertyChanged.Fody
```

#### Metoda 3: Pomocí .NET CLI (terminál)

Otevřete terminál v root složce projektu a spusťte:

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package PropertyChanged.Fody
```

### 3.4 Ověření instalace

Po instalaci otevřete soubor **WPF_Aplikace_s_db.csproj** (dvojklik na projekt v Solution Exploreru).

Měli byste vidět:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net10.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <UseWPF>true</UseWPF>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="10.x.x" /> <!-- Zde se nám nainstalovali nugety -->
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="10.x.x"> <!-- Zde se nám nainstalovali nugety -->
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
    <PackageReference Include="PropertyChanged.Fody" Version="4.x.x" /> <!-- Zde se nám nainstalovali nugety -->
  </ItemGroup>
</Project>
```

### 3.5 Co jednotlivé balíčky dělají?

#### **Microsoft.EntityFrameworkCore.SqlServer**

Entity Framework Core provider pro SQL Server. Umožňuje:
- Připojení k SQL Server / LocalDB
- Generování SQL dotazů
- Správu databázového schématu

#### **Microsoft.EntityFrameworkCore.Tools**

Nástroje pro práci s migraci v Package Manager Console:
- `Add-Migration` - Vytvoří novou migraci
- `Update-Database` - Aplikuje migrace na databázi
- `Remove-Migration` - Odstraní poslední migraci
- `Scaffold-DbContext` - Vytvoří model z existující databáze

#### **PropertyChanged.Fody**

Automaticky implementuje **INotifyPropertyChanged** interface. Tento interface je klíčový pro:
- **Data binding** - automatická aktualizace UI při změně dat
- **Two-way binding** - synchronizace UI a dat v obou směrech

**Bez PropertyChanged.Fody** byste museli psát:

```csharp
private string _firstName;
public string FirstName 
{ 
    get => _firstName;
    set 
    {
        _firstName = value;
        OnPropertyChanged(nameof(FirstName));
    }
}
```

**S PropertyChanged.Fody** stačí:

```csharp
[AddINotifyPropertyChangedInterface]
public class Student
{
    public string FirstName { get; set; }
}
```

### 📋 Shrnutí kapitoly 3
- Nainstalovali jsme 3 klíčové NuGet balíčky
- EntityFrameworkCore.SqlServer umožňuje práci s SQL Serverem
- EntityFrameworkCore.Tools poskytuje migrační nástroje
- PropertyChanged.Fody zjednodušuje data binding

### 💡 Cheat Sheet - NuGet příkazy (Package Manager Console)
| Příkaz | Akce |
|--------|------|
| `Install-Package <název>` | Instaluje balíček |
| `Update-Package <název>` | Aktualizuje balíček |
| `Uninstall-Package <název>` | Odinstaluje balíček |
| `Get-Package` | Zobrazí nainstalované balíčky |
| `Update-Package` | Aktualizuje všechny balíčky |

---

## Kapitola 4: Vytvoření struktury dat - Student entita

### 4.1 Vytvoření složky Data

1. V **Solution Exploreru** klikněte pravým tlačítkem na projekt
2. Vyberte **Add → New Folder**
3. Pojmenujte složku: `Data`

### 4.2 Vytvoření třídy Student

1. Klikněte pravým tlačítkem na složku **Data**
2. Vyberte **Add → Class...**
3. Pojmenujte soubor: `Student.cs`
4. Klikněte **Add**

### 4.3 Implementace třídy Student

Otevřete soubor `Student.cs` a nahraďte obsah následujícím kódem:

```csharp
using PropertyChanged;
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace WPF_Aplikace_s_db.Data
{
    [AddINotifyPropertyChangedInterface]
    public class Student
    {
        [Key]
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public int Id { get; set; }

        [Required]
        [StringLength(30)]
        public string FirstName { get; set; } = string.Empty;

        [Required]
        [StringLength(30)]
        public string LastName { get; set; } = string.Empty;

        [Range(1, 6)]
        public int Year { get; set; }

        [StringLength(50)]
        public string Email { get; set; } = string.Empty;

        public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    }
}
```

### 4.4 Detailní vysvětlení kódu

#### **Using direktivy**

```csharp
using PropertyChanged;                         // Pro [AddINotifyPropertyChangedInterface]
using System;                                  // Základní typy (DateTime, atd.)
using System.ComponentModel.DataAnnotations;   // Validační atributy
using System.ComponentModel.DataAnnotations.Schema; // Databázové atributy
```

#### **Namespace**

```csharp
namespace WPF_Aplikace_s_db.Data
```

- Logicky seskupuje třídy související s daty
- Zabraňuje konfliktům názvů
- Konvence: `NázevProjektu.Data`

#### **Atribut třídy: [AddINotifyPropertyChangedInterface]**

```csharp
[AddINotifyPropertyChangedInterface]
public class Student
```

**Co dělá:**
- Automaticky implementuje `INotifyPropertyChanged` interface
- Při změně jakékoliv property automaticky vyvolá událost `PropertyChanged`
- **Klíčové pro data binding** - UI se automaticky aktualizuje při změně dat

**Jak funguje:**
1. PropertyChanged.Fody analyzuje kód při kompilaci
2. Automaticky vygeneruje boilerplate kód pro každou property
3. Výsledek: Jednoduchý kód + plně funkční data binding

### 4.5 Databázové atributy (Data Annotations)

#### **[Key]** - Primární klíč

```csharp
[Key]
public int Id { get; set; }
```

- Označuje property jako **primární klíč** tabulky
- Každá entita musí mít primární klíč
- Obvykle se používá property s názvem `Id` nebo `{ClassName}Id`

#### **[DatabaseGenerated(DatabaseGeneratedOption.Identity)]**

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public int Id { get; set; }
```

**Co dělá:**
- Hodnota se **automaticky generuje databází**
- `Identity` = auto-increment (1, 2, 3, ...)
- Nemusíte ručně přiřazovat Id

**Další možnosti:**
- `None` - Hodnotu musíte přiřadit ručně
- `Computed` - Hodnota se vypočítá databází (např. computed columns)

#### **[Required]** - Povinné pole

```csharp
[Required]
public string FirstName { get; set; } = string.Empty;
```

- Property **nesmí být NULL**
- V databázi: `NOT NULL`
- Entity Framework validuje před uložením do DB

**Výchozí hodnota:**
```csharp
= string.Empty
```
- Zabraňuje `null` hodnotám
- Důležité pro nullable reference types (.NET 6+)

#### **[StringLength(max)]** - Maximální délka řetězce

```csharp
[StringLength(30)]
public string FirstName { get; set; }
```

- Omezuje **maximální délku řetězce**
- V databázi: `NVARCHAR(30)`
- Entity Framework validuje před uložením

**Rozšířená varianta:**
```csharp
[StringLength(30, MinimumLength = 2)]
```
- Přidává i minimální délku

#### **[Range(min, max)]** - Rozsah hodnot

```csharp
[Range(1, 6)]
public int Year { get; set; }
```

- Omezuje hodnotu na **rozsah 1-6**
- Funguje pro numerické typy
- Entity Framework validuje před uložením

**Použití:**
- Ročník studia (1-6)
- Věk (0-120)
- Hodnocení (1-5)

#### **DateTime s výchozí hodnotou**

```csharp
public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
```

- `DateTime.UtcNow` - aktuální čas v UTC (Coordinated Universal Time)
- Výchozí hodnota se nastaví při vytvoření instance
- **UTC vs Local:** UTC je nezávislé na časové zóně

### 4.6 Mapování na databázovou tabulku

Entita `Student` se namapuje na tabulku v databázi:

| C# Property | SQL Column | SQL Type | Constraints |
|-------------|------------|----------|-------------|
| `Id` | Id | INT | PRIMARY KEY, IDENTITY(1,1) |
| `FirstName` | FirstName | NVARCHAR(30) | NOT NULL |
| `LastName` | LastName | NVARCHAR(30) | NOT NULL |
| `Year` | Year | INT | CHECK (Year >= 1 AND Year <= 6) |
| `Email` | Email | NVARCHAR(50) | NULL |
| `CreatedAt` | CreatedAt | DATETIME2 | NOT NULL |

### 4.7 Další užitečné atributy

#### **[Column]** - Vlastní název sloupce

```csharp
[Column("student_first_name")]
public string FirstName { get; set; }
```

#### **[Table]** - Vlastní název tabulky

```csharp
[Table("tbl_students")]
public class Student
```

#### **[MaxLength]** vs **[StringLength]**

```csharp
[MaxLength(100)]  // Pouze maximální délka (DB level)
[StringLength(100)] // Validace i na client side
```

#### **[EmailAddress]** - Validace emailu

```csharp
[EmailAddress]
public string Email { get; set; }
```

#### **[Phone]** - Validace telefonu

```csharp
[Phone]
public string PhoneNumber { get; set; }
```

### 📋 Shrnutí kapitoly 4
- Vytvořili jsme entitní třídu `Student`
- Použili jsme Data Annotations pro validaci a mapování
- `[Key]` označuje primární klíč
- `[Required]` značí povinné pole
- `[StringLength]` omezuje délku řetězce
- `[Range]` omezuje číselné hodnoty
- PropertyChanged.Fody automatizuje INotifyPropertyChanged

### 💡 Cheat Sheet - Data Annotations
| Atribut | Účel | Příklad |
|---------|------|---------|
| `[Key]` | Primární klíč | `public int Id { get; set; }` |
| `[Required]` | Povinné pole (NOT NULL) | `[Required] public string Name { get; set; }` |
| `[StringLength(n)]` | Max délka řetězce | `[StringLength(50)] public string Email { get; set; }` |
| `[MaxLength(n)]` | Max délka (jen DB) | `[MaxLength(100)] public string Text { get; set; }` |
| `[Range(min, max)]` | Rozsah hodnot | `[Range(1, 100)] public int Age { get; set; }` |
| `[EmailAddress]` | Validace emailu | `[EmailAddress] public string Email { get; set; }` |
| `[Phone]` | Validace telefonu | `[Phone] public string Phone { get; set; }` |
| `[Url]` | Validace URL | `[Url] public string Website { get; set; }` |
| `[Column("name")]` | Vlastní název sloupce | `[Column("first_name")] public string FirstName` |
| `[Table("name")]` | Vlastní název tabulky | `[Table("students")] public class Student` |

---

## Kapitola 5: Databázový kontext - StudentContext

### 5.1 Co je DbContext?

**DbContext** je klíčová třída Entity Framework Core, která:
- Reprezentuje **session** s databází
- Umožňuje **dotazování** (LINQ queries)
- Umožňuje **ukládání** změn (SaveChanges)
- Spravuje **connection** k databázi
- **Trackuje změny** entit (Change Tracking)

### 5.2 Vytvoření třídy StudentContext

1. Klikněte pravým tlačítkem na složku **Data**
2. Vyberte **Add → Class...**
3. Pojmenujte soubor: `StudentContext.cs`
4. Klikněte **Add**

### 5.3 Implementace StudentContext

Otevřete `StudentContext.cs` a nahraďte obsah:

```csharp
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.Linq;

namespace WPF_Aplikace_s_db.Data
{
    public class StudentContext : DbContext
    {
        public DbSet<Student> Students => Set<Student>();

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            if (!optionsBuilder.IsConfigured)
            {
                optionsBuilder.UseSqlServer(
                    "Data Source=(localdb)\\MSSQLLocalDB;" +
                    "Initial Catalog=StudentDbDemo1;" +
                    "Integrated Security=True;" +
                    "TrustServerCertificate=True");
            }
        }

        public void EnsureCreatedAndSeed()
        {
            Database.EnsureCreated();
            SeedIfEmpty();
        }

        public void SeedIfEmpty()
        {
            if (!Students.Any())
            {
                var initial = new List<Student>
                {
                    new Student { FirstName="Jan",   LastName="Novák",    Year=1, Email="jan.novak@example.com" },
                    new Student { FirstName="Petr",  LastName="Svoboda",  Year=2, Email="petr.svoboda@example.com" },
                    new Student { FirstName="Karel", LastName="Černý",    Year=3, Email="karel.cerny@example.com" },
                    new Student { FirstName="Lucie", LastName="Malá",     Year=1, Email="lucie.mala@example.com" },
                    new Student { FirstName="Eva",   LastName="Bílá",     Year=2, Email="eva.bila@example.com" },
                    new Student { FirstName="Adam",  LastName="Zelený",   Year=3, Email="adam.zeleny@example.com" },
                    new Student { FirstName="Tomáš", LastName="Dvořák",   Year=1, Email="tomas.dvorak@example.com" },
                    new Student { FirstName="Marie", LastName="Veselá",   Year=2, Email="marie.vesela@example.com" },
                    new Student { FirstName="Jana",  LastName="Horáková", Year=3, Email="jana.horakova@example.com" },
                    new Student { FirstName="Filip", LastName="Král",     Year=1, Email="filip.kral@example.com" }
                };
                Students.AddRange(initial);
                SaveChanges();
            }
        }
    }
}
```

### 5.4 Detailní vysvětlení kódu

#### **Dědičnost z DbContext**

```csharp
public class StudentContext : DbContext
```

- `StudentContext` dědí z `DbContext`
- Získává všechny funkce EF Core
- Reprezentuje session s databází

#### **DbSet<T> property**

```csharp
public DbSet<Student> Students => Set<Student>();
```

**Co je DbSet:**
- Reprezentuje **tabulku v databázi**
- Umožňuje dotazování (LINQ queries)
- Umožňuje přidávání/odebírání záznamů

**Syntaxe:**
```csharp
=> Set<Student>()
```
- Expression-bodied member (zkrácený zápis)
- Ekvivalentní: `{ get { return Set<Student>(); } }`

**Použití:**
```csharp
var students = _db.Students.ToList();           // SELECT * FROM Students
_db.Students.Add(new Student {...});            // INSERT
_db.Students.Remove(student);                   // DELETE
_db.SaveChanges();                              // Uloží změny
```

### 5.5 OnConfiguring - Konfigurace připojení

#### **Metoda OnConfiguring**

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    if (!optionsBuilder.IsConfigured)
    {
        optionsBuilder.UseSqlServer(
            "Data Source=(localdb)\\MSSQLLocalDB;" +
            "Initial Catalog=StudentDbDemo1;" +
            "Integrated Security=True;" +
            "TrustServerCertificate=True");
    }
}
```

**Kdy se volá:**
- Automaticky před prvním použitím DbContext
- Konfiguruje připojení k databázi

**`if (!optionsBuilder.IsConfigured)`**
- Kontrola, zda již není nakonfigurováno
- Umožňuje přepsat konfiguraci z venku (dependency injection)

### 5.6 Connection String - Detailní rozbor

```
Data Source=(localdb)\MSSQLLocalDB;
Initial Catalog=StudentDbDemo1;
Integrated Security=True;
TrustServerCertificate=True
```

#### **Data Source=(localdb)\\MSSQLLocalDB**

**Co to znamená:**
- `(localdb)` - Použití **SQL Server LocalDB**
- `MSSQLLocalDB` - Název instance LocalDB

**Co je LocalDB:**
- Lehká verze SQL Serveru
- Běží **lokálně na vašem počítači**
- Automaticky se spouští při prvním použití
- **Ideální pro vývoj a testování**

**⚠️ DŮLEŽITÉ:**
- LocalDB je **lokální** k počítači
- Databáze je uložena v: `C:\Users\<username>\`
- Při přesunu projektu na **jiný počítač** se **databáze nepřenese**
- Musíte vytvořit databázi znovu (viz kapitola o migracích)

**Alternativy:**
```csharp
// Plný SQL Server
"Data Source=localhost;..."

// SQL Server Express
"Data Source=.\\SQLEXPRESS;..."

// Vzdálený server
"Data Source=192.168.1.100;..."
```

#### **Initial Catalog=StudentDbDemo1**

**Co to znamená:**
- `Initial Catalog` = název databáze
- V tomto případě: `StudentDbDemo1`

**Změna názvu databáze:**
```csharp
"Initial Catalog=MojaSkola;"  // Databáze se bude jmenovat "MojaSkola"
```

#### **Integrated Security=True**

**Co to znamená:**
- Použití **Windows Authentication**
- Přihlášení pomocí aktuálního Windows účtu
- **Bez nutnosti zadávat username/password**

**Alternativa (SQL Server Authentication):**
```csharp
"User Id=sa;Password=mojeheslo123;Integrated Security=False;"
```

#### **TrustServerCertificate=True**

**Co to znamená:**
- Důvěřuj SSL certifikátu serveru bez validace
- **Nutné pro LocalDB** (nemá platný certifikát)
- V produkci by mělo být `False` (bezpečnostní riziko)

### 5.7 Kam se databáze ukládá?

#### **LocalDB - Umístění souborů**

Databázové soubory LocalDB se ukládají v:

```
C:\Users\<VašeJméno>\
```

Konkrétní soubory:
- `StudentDbDemo1.mdf` - Hlavní databázový soubor
- `StudentDbDemo1_log.ldf` - Log soubor

#### **Jak najít umístění databáze**

**SQL Server Management Studio (SSMS):**
1. Připojte se k `(localdb)\MSSQLLocalDB`
2. Pravý klik na databázi → Properties
3. Záložka "Files"

**SQL dotaz:**
```sql
SELECT physical_name 
FROM sys.master_files 
WHERE database_id = DB_ID('StudentDbDemo1')
```

### 5.8 Proč se databáze nepřenese?

**Scénář:**
1. Vytvoříte projekt na počítači A
2. Databáze se vytvoří v `C:\Users\Student1\`
3. Nahrajete projekt na GitHub
4. Stáhnete projekt na počítač B

**Problém:**
- Databáze **není součástí projektu**
- Je uložená lokálně na počítači A
- Počítač B nemá databázi

**Řešení:**
- Použít **migrace** (kapitola 7)
- Automatické vytvoření databáze pomocí `EnsureCreated()` (viz níže)

### 5.9 EnsureCreatedAndSeed - Vytvoření DB

```csharp
public void EnsureCreatedAndSeed()
{
    Database.EnsureCreated();
    SeedIfEmpty();
}
```

#### **Database.EnsureCreated()**

**Co dělá:**
- Zkontroluje, zda databáze existuje
- Pokud **neexistuje**, vytvoří ji
- Vytvoří všechny tabulky podle entit (Student)

**⚠️ POZOR:**
- Nepoužívejte s **migraci**!
- `EnsureCreated()` a migrace se **nesmí kombinovat**
- Pro produkci použijte migrace (kapitola 7)

**Vhodné pro:**
- Prototypování
- Demo aplikace
- Výukové projekty

### 📋 Shrnutí kapitoly 5
- `DbContext` reprezentuje session s databází
- `DbSet<T>` reprezentuje tabulku
- `OnConfiguring` konfiguruje připojení
- LocalDB je lokální k počítači (nepřenáší se)
- Connection string obsahuje server, databázi a autentizaci
- `EnsureCreated()` automaticky vytvoří databázi (ne pro produkci)

### 💡 Cheat Sheet - DbContext
| Metoda/Property | Účel |
|-----------------|------|
| `DbSet<T>` | Tabulka v databázi |
| `SaveChanges()` | Uložit změny do DB |
| `Add(entity)` | Přidat záznam |
| `Remove(entity)` | Smazat záznam |
| `Update(entity)` | Aktualizovat záznam |
| `Find(id)` | Najít podle primárního klíče |
| `AsNoTracking()` | Vypnout change tracking (rychlejší read-only) |
| `Database.EnsureCreated()` | Vytvořit DB, pokud neexistuje |
| `Database.Migrate()` | Aplikovat migrace |

---

## Kapitola 6: Seedování dat

### 6.1 Co je seedování?

**Seedování (seeding)** = Naplnění databáze **testovacími/výchozími daty**.

**Účel:**
- Vývoj - testovací data pro lokální testování
- Demo - ukázková data pro prezentaci
- Produkce - výchozí konfigurace, role, atd.

### 6.2 Implementace seedování

```csharp
public void SeedIfEmpty()
{
    if (!Students.Any())
    {
        var initial = new List<Student>
        {
            new Student { FirstName="Jan",   LastName="Novák",    Year=1, Email="jan.novak@example.com" },
            new Student { FirstName="Petr",  LastName="Svoboda",  Year=2, Email="petr.svoboda@example.com" },
            // ... další studenti
        };
        Students.AddRange(initial);
        SaveChanges();
    }
}
```

### 6.3 Detailní vysvětlení

#### **Kontrola prázdné tabulky**

```csharp
if (!Students.Any())
```

- `Any()` - LINQ metoda, vrací `true`, pokud existuje alespoň 1 záznam
- `!` - negace, tj. pokud **neexistuje žádný** záznam
- **Důvod:** Chceme seedovat pouze jednou (prázdnou databázi)

**Ekvivalentní SQL:**
```sql
SELECT COUNT(*) FROM Students
```

#### **Vytvoření testovacích dat**

```csharp
var initial = new List<Student>
{
    new Student 
    { 
        FirstName="Jan", 
        LastName="Novák", 
        Year=1, 
        Email="jan.novak@example.com" 
    },
    // ...
};
```

**Collection initializer syntax:**
- Vytvoří `List<Student>`
- Inicializuje objekty pomocí **object initializer** syntaxe

**Poznámka:**
- `Id` **neuvádíme** (auto-generované databází)
- `CreatedAt` **neuvádíme** (výchozí hodnota `DateTime.UtcNow`)

#### **Přidání do kontextu**

```csharp
Students.AddRange(initial);
```

**AddRange:**
- Přidá **více** záznamů najednou
- Efektivnější než volat `Add()` 10x

**Alternativa:**
```csharp
foreach (var student in initial)
{
    Students.Add(student);
}
```

#### **Uložení do databáze**

```csharp
SaveChanges();
```

**Co se stane:**
1. EF Core vygeneruje SQL INSERT příkazy
2. Odešle je do databáze
3. Databáze vrátí vygenerované `Id`
4. EF Core aktualizuje `Id` u objektů

**Ekvivalentní SQL:**
```sql
INSERT INTO Students (FirstName, LastName, Year, Email, CreatedAt)
VALUES ('Jan', 'Novák', 1, 'jan.novak@example.com', GETDATE())
```

### 6.4 Pokročilé seedování

#### **HasData - Deklarativní seedování**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>().HasData(
        new Student { Id = 1, FirstName = "Jan", LastName = "Novák", Year = 1 },
        new Student { Id = 2, FirstName = "Petr", LastName = "Svoboda", Year = 2 }
    );
}
```

**Výhody:**
- Součástí migrací
- Automaticky se aplikuje při `Update-Database`

**Nevýhody:**
- Musíte ručně přiřadit `Id`
- Méně flexibilní

#### **Seedování z JSON souboru**

```csharp
public void SeedFromJson()
{
    var json = File.ReadAllText("students.json");
    var students = JsonSerializer.Deserialize<List<Student>>(json);
    Students.AddRange(students);
    SaveChanges();
}
```

### 6.5 Best practices pro seedování

#### ✅ DOPORUČENO

```csharp
// 1. Kontrola, zda již data existují
if (!Students.Any())
{
    // seedování
}

// 2. Použití konstantních dat
public static class SeedData
{
    public static List<Student> GetInitialStudents()
    {
        return new List<Student> { /* ... */ };
    }
}

// 3. Seedování v samostatné metodě
public void SeedDatabase()
{
    SeedStudents();
    SeedCourses();
    SeedEnrollments();
}
```

#### ❌ NEDOPORUČENO

```csharp
// Vždy přidávat data (duplicity!)
Students.AddRange(initial);
SaveChanges();

// Seedování přímo v OnConfiguring
protected override void OnConfiguring(...)
{
    // NIKDY seedovat zde!
}
```

### 6.6 Kdy se seed spouští?

V našem projektu:

```csharp
// MainWindow.xaml.cs - konstruktor
public MainWindow()
{
    InitializeComponent();
    
    _db.EnsureCreatedAndSeed();  // <-- TADY
    
    // ...
}
```

**Děj:**
1. Aplikace se spustí
2. `MainWindow` konstruktor se zavolá
3. `EnsureCreatedAndSeed()` vytvoří DB (pokud neexistuje)
4. `SeedIfEmpty()` naplní daty (pokud jsou prázdné)

### 📋 Shrnutí kapitoly 6
- Seedování naplňuje databázi testovacími daty
- `Any()` kontroluje, zda tabulka obsahuje záznamy
- `AddRange()` přidává více záznamů najednou
- `SaveChanges()` uloží změny do databáze
- Seed spouštíme při startu aplikace

### 💡 Cheat Sheet - LINQ metody pro seedování
| Metoda | Použití | Příklad |
|--------|---------|---------|
| `Any()` | Obsahuje záznamy? | `if (!Students.Any())` |
| `Count()` | Počet záznamů | `if (Students.Count() == 0)` |
| `AddRange()` | Přidat více | `Students.AddRange(list)` |
| `RemoveRange()` | Smazat více | `Students.RemoveRange(list)` |
| `ToList()` | Načíst všechny | `var all = Students.ToList()` |

---

## Kapitola 7: Databázové migrace - Teorie a praxe

### 7.1 Co jsou databázové migrace?

**Databázové migrace** jsou mechanismus pro **verzování a správu databázového schématu**. Fungují podobně jako Git pro kód, ale pro strukturu databáze.

**Základní koncept:**
- Každá změna schématu = nová migrace (soubor)
- Migrace obsahuje **změny** a **rollback**
- Historie všech změn databáze
- Možnost aplikovat/vrátit změny

**Proč migrace?**
1. ✅ **Verzování** - Historie všech změn schématu
2. ✅ **Kolaborace** - Team sdílí stejné změny
3. ✅ **Automatizace** - Automatické upgrady při nasazení
4. ✅ **Rollback** - Možnost vrátit změny zpět
5. ✅ **Dokumentace** - Vidíte, jak se DB vyvíjela

### 7.2 EnsureCreated vs Migrace - Zásadní rozdíl

Náš projekt momentálně používá `EnsureCreated()`, což je **vhodné pouze pro prototypování**.

#### **Database.EnsureCreated() - Současný přístup**

```csharp
// V MainWindow konstruktoru
_db.EnsureCreatedAndSeed();

// V StudentContext
public void EnsureCreatedAndSeed()
{
    Database.EnsureCreated();  // <-- Vytvoří DB pokud neexistuje
    SeedIfEmpty();
}
```

**Co dělá:**
- Zkontroluje, zda databáze existuje
- Pokud ne, vytvoří ji podle současného stavu entit
- **DŮLEŽITÉ:** Nevytváří migrační historii!

**✅ Výhody:**
- Jednoduché na použití
- Rychlé pro prototypy
- Ideální pro demo a učení

**❌ Nevýhody:**
- Žádná historie změn
- Nemůžete **aktualizovat** existující databázi
- Pokud změníte entitu, musíte **smazat a znovu vytvořit** DB
- **Ne pro produkci**

#### **Database.Migrate() - Produkční přístup**

```csharp
// V MainWindow konstruktoru
_db.Database.Migrate();  // <-- Aplikuje migrace
_db.SeedIfEmpty();
```

**Co dělá:**
- Zkontroluje tabulku `__EFMigrationsHistory`
- Zjistí, které migrace ještě nebyly aplikovány
- Postupně aplikuje všechny čekající migrace

**✅ Výhody:**
- Kompletní historie změn
- Bezpečné aktualizace existující DB
- Team collaboration
- Možnost rollbacku
- **Vhodné pro produkci**

**❌ Nevýhody:**
- Komplexnější nastavení
- Vyžaduje učení příkazů

### 7.3 NuGet balíčky pro migrace - Detailní rozbor

Migrace vyžadují **dva NuGet balíčky**, které jsme již nainstalovali v kapitole 3:

#### **1. Microsoft.EntityFrameworkCore.SqlServer**

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="9.0.9" />
```

**Co tento balíček obsahuje:**

1. **SQL Server Database Provider**
   - Implementuje `UseSqlServer()` metodu
   - Generuje T-SQL specifické dotazy
   - Podporuje SQL Server specifické funkce (IDENTITY, SEQUENCES, atd.)

2. **Connection Management**
   - Správa connection poolů
   - Automatické otevírání/zavírání spojení
   - Retry logika při chybách

3. **Type Mapping**
   - Mapuje C# typy na SQL Server typy:
     - `string` → `NVARCHAR(MAX)` nebo `NVARCHAR(n)`
     - `int` → `INT`
     - `DateTime` → `DATETIME2`
     - `bool` → `BIT`
     - `decimal` → `DECIMAL(18,2)`

4. **SQL Generation pro migrace**
   - Vytváří SQL skripty z migračních příkazů
   - Optimalizuje dotazy pro SQL Server

**Proč je nutný pro migrace:**
- Migrace potřebují vědět, jak vytvořit SQL příkazy pro konkrétní databázový systém
- Tento provider "překládá" generické EF Core příkazy na SQL Server T-SQL

**Příklad:**
```csharp
// EF Core příkaz v migraci
migrationBuilder.CreateTable(
    name: "Students",
    columns: table => new
    {
        Id = table.Column<int>(nullable: false)
            .Annotation("SqlServer:Identity", "1, 1")
    });

// Vygeneruje SQL
CREATE TABLE [Students] (
    [Id] INT NOT NULL IDENTITY(1,1)
);
```

#### **2. Microsoft.EntityFrameworkCore.Tools**

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="9.0.9">
  <PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
</PackageReference>
```

**Co tento balíček obsahuje:**

1. **Package Manager Console příkazy**
   - `Add-Migration`
   - `Update-Database`
   - `Remove-Migration`
   - `Script-Migration`
   - `Drop-Database`
   - `Get-DbContext`

2. **Design-time služby**
   - Analyzuje DbContext a entity
   - Detekuje změny v modelu
   - Generuje migrační kód

3. **Scaffolding engine**
   - Vytváří migrační soubory
   - Generuje C# kód pro Up/Down metody

**Speciální atributy v .csproj:**

```xml
<PrivateAssets>all</PrivateAssets>
```
- Balíček je **pouze pro vývoj** (design-time)
- Není součástí publikované aplikace
- Šetří velikost výsledného .exe

```xml
<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
```
- Které assety balíčku se použijí
- `analyzers` - Code analyzátory
- `build` - Build-time nástroje

**Proč jsou Tools oddělené od SqlServer:**
- Tools fungují pro **všechny providery** (SQL Server, PostgreSQL, MySQL, SQLite)
- SqlServer je specifický pro SQL Server

### 7.4 Jak migrace fungují - Pod pokličkou

#### **Architektura migračního systému**

```
┌──────────────────────────────────────────────────────────────┐
│  1. Vaše entity (Student.cs)                                 │
└───────────────┬──────────────────────────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│  2. Add-Migration příkaz (PMC)                               │
│     - EF.Tools analyzuje DbContext                           │
│     - Porovná aktuální model se Snapshot                     │
│     - Detekuje rozdíly (Diff)                                │
└───────────────┬──────────────────────────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│  3. Vygeneruje migrační soubory                              │
│     - YYYYMMDDHHMMSS_MigrationName.cs                        │
│     - YYYYMMDDHHMMSS_MigrationName.Designer.cs               │
│     - StudentContextModelSnapshot.cs (aktualizace)           │
└───────────────┬──────────────────────────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│  4. Update-Database příkaz                                   │
│     - Načte __EFMigrationsHistory z DB                       │
│     - Zjistí, které migrace chybí                            │
│     - Provede Up() metody chybějících migrací                │
└───────────────┬──────────────────────────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│  5. Databáze aktualizována                                   │
│     - Nové tabulky/sloupce vytvořeny                         │
│     - Záznam v __EFMigrationsHistory                         │
└──────────────────────────────────────────────────────────────┘
```

#### **Tabulka __EFMigrationsHistory**

EF Core automaticky vytváří speciální tabulku:

```sql
CREATE TABLE [__EFMigrationsHistory] (
    [MigrationId] NVARCHAR(150) NOT NULL PRIMARY KEY,
    [ProductVersion] NVARCHAR(32) NOT NULL
);
```

**Příklad obsahu:**
```
MigrationId                      | ProductVersion
---------------------------------|---------------
20260215_120000_InitialCreate    | 9.0.9
20260216_100000_AddPhoneNumber   | 9.0.9
20260217_143000_AddCourseTable   | 9.0.9
```

**Účel:**
- EF Core ví, které migrace již byly aplikovány
- Při `Update-Database` aplikuje pouze nové

### 7.5 Cesta dat z kódu do databáze - Vizualizace

#### **Tok dat při SaveChanges()**

```
┌─────────────────────────────────────────────────────────────────┐
│  1. APLIKACE: Změna entity v paměti                             │
│                                                                 │
│     var student = _db.Students.First();                         │
│     student.FirstName = "Karel";  // Změna property             │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  2. CHANGE TRACKER: Detekce změn                                │
│                                                                 │
│     EF Core si pamatuje původní hodnoty:                        │
│     - Original: FirstName = "Jan"                               │
│     - Current:  FirstName = "Karel"                             │
│     → Stav entity: MODIFIED                                     │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  3. SAVECHANGES(): Příprava příkazů                             │
│                                                                 │
│     _db.SaveChanges();                                          │
│                                                                 │
│     EF Core:                                                    │
│     - Projde všechny tracked entity                             │
│     - Identifikuje změny (Added, Modified, Deleted)             │
│     - Vygeneruje SQL příkazy                                    │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  4. SQL SERVER PROVIDER: Generování T-SQL                       │
│                                                                 │
│     UPDATE [Students]                                           │
│     SET [FirstName] = @p0                                       │
│     WHERE [Id] = @p1;                                           │
│     SELECT @@ROWCOUNT;                                          │
│                                                                 │
│     Parametry:                                                  │
│     @p0 = 'Karel'                                               │
│     @p1 = 1                                                     │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  5. ADO.NET: Otevření connection                                │
│                                                                 │
│     Connection String:                                          │
│     "Data Source=(localdb)\\MSSQLLocalDB;..."                   │
│                                                                 │
│     Connection Pool → Získá volné spojení nebo vytvoří nové     │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  6. LOCALDB: Přijetí požadavku                                  │
│                                                                 │
│     SQL Server LocalDB proces:                                  │
│     C:\Users\<username>\AppData\Local\Microsoft\                │
│       Microsoft SQL Server Local DB\Instances\MSSQLLocalDB\     │
│                                                                 │
│     Instance automaticky startuje (pokud nebyla spuštěná)       │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  7. DATABÁZOVÝ SOUBOR: Zápis dat                                │
│                                                                 │
│     Umístění:                                                   │
│     C:\Users\<username>\StudentDbDemo1.mdf (data)               │
│     C:\Users\<username>\StudentDbDemo1_log.ldf (log)            │
│                                                                 │
│     SQL Engine:                                                 │
│     - Zamkne řádek (row lock)                                   │
│     - Zapíše změnu do data page                                 │
│     - Zapíše transakci do log souboru                           │
│     - Odemkne řádek                                             │
│                                                                 │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  8. NÁVRAT DO APLIKACE                                          │
│                                                                 │
│     SaveChanges() vrací počet změněných záznamů                 │
│     EF Core aktualizuje Change Tracker:                         │
│     - Entity přejdou do stavu "Unchanged"                       │
│     - Original hodnoty = Current hodnoty                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 7.6 Fyzické umístění databáze - Detailní průvodce

#### **Kde se LocalDB databáze fyzicky nachází?**

**Výchozí umístění:**
```
C:\Users\<VašeUživatelskeJméno>\
├── StudentDbDemo1.mdf       # Hlavní databázový soubor (Data)
└── StudentDbDemo1_log.ldf   # Transaction log soubor
```

**Příklad pro uživatele "student":**
```
C:\Users\student\StudentDbDemo1.mdf
C:\Users\student\StudentDbDemo1_log.ldf
```

#### **Jak najít přesné umístění?**

**Metoda 1: SQL dotaz**

```sql
-- Připojte se k (localdb)\MSSQLLocalDB v SSMS
SELECT 
    name AS 'Database Name',
    physical_name AS 'File Path',
    type_desc AS 'File Type',
    size * 8 / 1024 AS 'Size (MB)'
FROM sys.master_files 
WHERE database_id = DB_ID('StudentDbDemo1');
```

**Výsledek:**
```
Database Name  | File Path                                    | File Type  | Size (MB)
---------------|----------------------------------------------|------------|----------
StudentDbDemo1 | C:\Users\student\StudentDbDemo1.mdf          | ROWS       | 8
StudentDbDemo1 | C:\Users\student\StudentDbDemo1_log.ldf      | LOG        | 8
```

**Metoda 2: SSMS Properties**

1. Otevřete SQL Server Management Studio (SSMS)
2. Připojte se k `(localdb)\MSSQLLocalDB`
3. Rozbalte **Databases**
4. Pravý klik na **StudentDbDemo1** → **Properties**
5. Záložka **Files**
6. Vidíte sloupec "Path"

**Metoda 3: PowerShell**

```powershell
# Najdi všechny .mdf soubory v uživatelském profilu
Get-ChildItem -Path $env:USERPROFILE -Filter "StudentDbDemo1.mdf" -Recurse -ErrorAction SilentlyContinue
```

#### **Co obsahuje .mdf soubor?**

**StudentDbDemo1.mdf** (Data File):
- Databázové tabulky a jejich data
- Indexy
- Stored procedures
- Views
- Triggers
- Metadata (schéma)

**Struktura uvnitř .mdf:**

```
StudentDbDemo1.mdf
├── System Tables
│   ├── sys.tables              # Metadata o tabulkách
│   ├── sys.columns             # Metadata o sloupcích
│   └── __EFMigrationsHistory   # Historie migrací EF Core
│
└── User Tables
    └── dbo.Students            # Naše tabulka
        ├── Data Pages          # Aktuální data (řádky)
        ├── Index Pages         # Indexy pro rychlé vyhledávání
        └── Clustered Index     # Primary Key (Id)
```

**StudentDbDemo1_log.ldf** (Log File):
- Transaction log (záznamy všech transakcí)
- Umožňuje rollback a recovery
- Umožňuje point-in-time restore

#### **Proč se databáze nepřenese na jiný počítač?**

**Scénář:**
```
┌──────────────────────────────────────────────────────┐
│  POČÍTAČ A (Váš vývojový počítač)                    │
│                                                      │
│  Projekt:                                            │
│  C:\Projects\WPF_Aplikace_s_db\                      │
│  ├── Data\                                           │
│  ├── MainWindow.xaml                                 │
│  └── ...                                             │
│                                                      │
│  Databáze:                                           │
│  C:\Users\student_a\StudentDbDemo1.mdf  ← TADY!      │
│                                                      │
└──────────────────────────────────────────────────────┘
                         │
                         │ Git push / ZIP / USB
                         ▼
┌──────────────────────────────────────────────────────┐
│  POČÍTAČ B (Jiný počítač)                            │
│                                                      │
│  Projekt:                                            │
│  D:\School\WPF_Aplikace_s_db\                        │
│  ├── Data\                                           │
│  ├── MainWindow.xaml                                 │
│  └── ...                    ✅ PŘENESEN               │
│                                                      │
│  Databáze:                                           │
│  ??? CHYBÍ!                 ❌ NEPŘENESEN             │
│                                                      │
└──────────────────────────────────────────────────────┘
```

**Důvody:**
1. **.mdf soubor není součástí projektu**
   - Je v user profilu (`C:\Users\...`)
   - Projekt je v jiné složce (`C:\Projects\...`)
   - Git/ZIP neobsahuje soubory mimo projekt

2. **Connection string ukazuje na lokální instanci**
   ```csharp
   "Data Source=(localdb)\\MSSQLLocalDB;..."
   ```
   - Každý počítač má **svou vlastní** LocalDB instanci
   - Nesdílí se mezi počítači

3. **Uživatelské cesty jsou různé**
   - Počítač A: `C:\Users\student_a\`
   - Počítač B: `C:\Users\student_b\`

### 7.7 Řešení problému přenositelnosti

#### **Řešení 1: Migrace (DOPORUČENO pro další cvičení)**

```csharp
// Místo EnsureCreated()
_db.Database.Migrate();
```

**Postup:**
1. Vytvoříte migrace na počítači A
2. Migrace commitnete do Gitu (jsou součástí projektu!)
3. Na počítači B stáhnete projekt včetně migrací
4. Spustíte aplikaci → `Migrate()` vytvoří DB automaticky

**Struktura projektu s migracemi:**
```
WPF_Aplikace_s_db/
├── Migrations/                          ← V Gitu!
│   ├── 20260215_InitialCreate.cs
│   ├── 20260216_AddPhoneNumber.cs
│   └── StudentContextModelSnapshot.cs
├── Data/
└── ...
```

#### **Řešení 2: EnsureCreated() (současný přístup)**

```csharp
_db.EnsureCreatedAndSeed();
```

**Chování na novém počítači:**
1. Aplikace se spustí
2. `EnsureCreated()` zjistí, že DB neexistuje
3. **Automaticky vytvoří novou databázi**
4. `SeedIfEmpty()` naplní testovacími daty

**⚠️ DŮLEŽITÉ:**
- Funguje **pouze pro prázdné/nové databáze**
- Pokud DB existuje, ale má **staré schéma**, EnsureCreated() **nedělá nic**!
- Museli byste **ručně smazat** starou DB

#### **Řešení 3: Database Backup/Restore**

**Export databáze:**
```sql
BACKUP DATABASE StudentDbDemo1
TO DISK = 'C:\Backups\StudentDbDemo1.bak'
WITH FORMAT, INIT, NAME = 'Full Backup';
```

**Import na jiném počítači:**
```sql
RESTORE DATABASE StudentDbDemo1
FROM DISK = 'C:\Backups\StudentDbDemo1.bak'
WITH MOVE 'StudentDbDemo1' TO 'C:\Users\new_user\StudentDbDemo1.mdf',
     MOVE 'StudentDbDemo1_log' TO 'C:\Users\new_user\StudentDbDemo1_log.ldf';
```

### 7.8 Migrační příkazy - Kompletní reference

#### **Package Manager Console (PMC) příkazy**

Otevřete **Package Manager Console**:
- Menu: **Tools → NuGet Package Manager → Package Manager Console**

---

#### **1. Add-Migration - Vytvoření migrace**

```powershell
Add-Migration <MigrationName>
```

**Co dělá:**
1. Analyzuje aktuální DbContext a entity
2. Porovná s posledním snapshot
3. Detekuje změny (nové tabulky, sloupce, změny typů, atd.)
4. Vygeneruje tři soubory:
   - `YYYYMMDDHHMMSS_<MigrationName>.cs` - Up/Down metody
   - `YYYYMMDDHHMMSS_<MigrationName>.Designer.cs` - Metadata
   - `StudentContextModelSnapshot.cs` - Aktuální snapshot (update)

**Příklady:**
```powershell
Add-Migration InitialCreate
Add-Migration AddPhoneNumberToStudent
Add-Migration CreateCoursesTable
Add-Migration RenameEmailColumn
```

**⚠️ Chyby:**
```powershell
# Špatně - mezery bez uvozovek
Add-Migration Add Phone Number

# Správně - bez mezer nebo s uvozovkami
Add-Migration AddPhoneNumber
Add-Migration "Add Phone Number"
```

**Volitelné parametry:**
```powershell
# Specifikovat konkrétní DbContext (pokud máte více)
Add-Migration InitialCreate -Context StudentContext

# Specifikovat složku pro migrace
Add-Migration InitialCreate -OutputDir Data/Migrations
```

---

#### **2. Update-Database - Aplikace migrací**

```powershell
Update-Database
```

**Co dělá:**
1. Připojí se k databázi (connection string z OnConfiguring)
2. Zkontroluje tabulku `__EFMigrationsHistory`
3. Zjistí, které migrace ještě nebyly aplikovány
4. Provede `Up()` metody všech chybějících migrací **postupně**
5. Po každé migraci zapíše záznam do `__EFMigrationsHistory`

**Příklad - první spuštění:**
```
Migrace v projektu:
- 20260215_InitialCreate
- 20260216_AddPhoneNumber

__EFMigrationsHistory je prázdná

→ Aplikuje InitialCreate (vytvoří tabulku Students)
→ Aplikuje AddPhoneNumber (přidá sloupec PhoneNumber)
```

**Volitelné parametry:**
```powershell
# Vrátit na konkrétní migraci
Update-Database -Migration AddPhoneNumber

# Vrátit na začátek (smaže všechny tabulky)
Update-Database -Migration 0

# Specifikovat DbContext
Update-Database -Context StudentContext

# Zobrazit SQL bez aplikace
Update-Database -Script
```

---

#### **3. Remove-Migration - Odstranění migrace**

```powershell
Remove-Migration
```

**Co dělá:**
- Smaže **poslední** vytvořenou migraci
- Obnoví předchozí snapshot

**⚠️ FUNGUJE POUZE:**
- Pokud migrace **nebyla aplikována** (`Update-Database`)
- Jinak: Chyba `The migration '...' has already been applied to the database.`

**Řešení, pokud byla aplikována:**
```powershell
# 1. Vrátit DB na předchozí stav
Update-Database -Migration PreviousMigration

# 2. Teď můžete odstranit
Remove-Migration
```

---

#### **4. Script-Migration - Generování SQL skriptu**

```powershell
Script-Migration
```

**Co dělá:**
- Vygeneruje **SQL skript** pro všechny migrace
- Neaplikuje na databázi (pouze preview)

**Příklady:**
```powershell
# Skript pro všechny migrace
Script-Migration

# Skript od konkrétní migrace do jiné
Script-Migration -From InitialCreate -To AddPhoneNumber

# Skript jen pro jednu migraci
Script-Migration -From AddPhoneNumber -To AddPhoneNumber

# Uložit do souboru
Script-Migration -Output C:\migration.sql

# Idempotentní skript (kontroluje, zda už není aplikováno)
Script-Migration -Idempotent
```

**Výstup:**
```sql
IF NOT EXISTS(SELECT * FROM [__EFMigrationsHistory] WHERE [MigrationId] = N'20260215_InitialCreate')
BEGIN
    CREATE TABLE [Students] (
        [Id] INT NOT NULL IDENTITY(1,1),
        [FirstName] NVARCHAR(30) NOT NULL,
        ...
    );
    
    INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
    VALUES (N'20260215_InitialCreate', N'9.0.9');
END;
```

**Použití:**
- Preview změn před aplikací
- Ruční aplikace na produkčním serveru
- Code review databázových změn

---

#### **5. Drop-Database - Smazání databáze**

```powershell
Drop-Database
```

**Co dělá:**
- **Kompletně smaže** databázi
- Ekvivalent: `DROP DATABASE StudentDbDemo1`

**⚠️ POZOR:**
- Nevratná operace!
- Přijdete o všechna data!

**Použití:**
```powershell
# Smaže databázi
Drop-Database

# Poté vytvoříte čistou DB
Update-Database
```

---

#### **6. Get-DbContext - Seznam DbContextů**

```powershell
Get-DbContext
```

**Co dělá:**
- Zobrazí všechny DbContext třídy v projektu
- Užitečné pokud máte více kontextů

**Výstup:**
```
WPF_Aplikace_s_db.Data.StudentContext
```

---

#### **7. Get-Migration - Seznam migrací**

```powershell
Get-Migration
```

**Co dělá:**
- Zobrazí všechny migrace v projektu
- **Neověřuje**, zda byly aplikovány na DB

**Výstup:**
```
20260215120000_InitialCreate
20260216100000_AddPhoneNumber
20260217143000_AddCourseTable
```

---

### 7.9 .NET CLI příkazy - Alternativa k PMC

Pokud preferujete **Command Line**, můžete použít .NET CLI místo Package Manager Console.

#### **Instalace EF Core tools**

```bash
dotnet tool install --global dotnet-ef
```

**Ověření instalace:**
```bash
dotnet ef --version
```

#### **Migrační příkazy v .NET CLI**

| PMC příkaz | .NET CLI příkaz |
|------------|-----------------|
| `Add-Migration Name` | `dotnet ef migrations add Name` |
| `Update-Database` | `dotnet ef database update` |
| `Remove-Migration` | `dotnet ef migrations remove` |
| `Script-Migration` | `dotnet ef migrations script` |
| `Drop-Database` | `dotnet ef database drop` |
| `Get-DbContext` | `dotnet ef dbcontext list` |

**Příklady:**
```bash
# Vytvoření migrace
dotnet ef migrations add InitialCreate

# Aplikace migrací
dotnet ef database update

# Generování SQL skriptu
dotnet ef migrations script -o migration.sql

# Odstranění migrace
dotnet ef migrations remove

# Smazání databáze
dotnet ef database drop
```

### 7.10 Praktický workflow - Krok za krokem

#### **Scénář: Přidání nového sloupce PhoneNumber**

**Krok 1: Upravit entitu**

```csharp
// Data/Student.cs
public class Student
{
    // ... existující properties
    
    [Phone]
    [StringLength(20)]
    public string PhoneNumber { get; set; } = string.Empty;  // ← NOVÝ SLOUPEC
}
```

**Krok 2: Vytvořit migraci**

```powershell
Add-Migration AddPhoneNumberToStudent
```

**Výstup v konzoli:**
```
Build started...
Build succeeded.
To undo this action, use Remove-Migration.
```

**Vytvořené soubory:**
```
Migrations/
└── 20260215143000_AddPhoneNumberToStudent.cs
└── 20260215143000_AddPhoneNumberToStudent.Designer.cs
```

**Krok 3: Prohlédnout vygenerovaný kód**

```csharp
// Migrations/20260215143000_AddPhoneNumberToStudent.cs
public partial class AddPhoneNumberToStudent : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>(
            name: "PhoneNumber",
            table: "Students",
            type: "nvarchar(20)",
            maxLength: 20,
            nullable: false,
            defaultValue: "");
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(
            name: "PhoneNumber",
            table: "Students");
    }
}
```

**Krok 4: Aplikovat migraci**

```powershell
Update-Database
```

**Výstup:**
```
Build started...
Build succeeded.
Applying migration '20260215143000_AddPhoneNumberToStudent'.
Done.
```

**Co se stalo v databázi:**

```sql
-- 1. Přidán sloupec
ALTER TABLE [Students] ADD [PhoneNumber] NVARCHAR(20) NOT NULL DEFAULT N'';

-- 2. Záznam v historii
INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20260215143000_AddPhoneNumberToStudent', N'9.0.9');
```

**Krok 5: Ověření v SSMS**

```sql
-- Zkontrolovat strukturu tabulky
EXEC sp_help 'Students';

-- Nebo
SELECT COLUMN_NAME, DATA_TYPE, CHARACTER_MAXIMUM_LENGTH, IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'Students';
```

**Výsledek:**
```
COLUMN_NAME  | DATA_TYPE | CHARACTER_MAXIMUM_LENGTH | IS_NULLABLE
-------------|-----------|--------------------------|------------
Id           | int       | NULL                     | NO
FirstName    | nvarchar  | 30                       | NO
LastName     | nvarchar  | 30                       | NO
Year         | int       | NULL                     | NO
Email        | nvarchar  | 50                       | NO
CreatedAt    | datetime2 | NULL                     | NO
PhoneNumber  | nvarchar  | 20                       | NO          ← NOVÝ!
```

### 7.11 Rollback migrace - Vrácení změn

#### **Vrátit poslední migraci**

```powershell
Update-Database -Migration PreviousMigrationName
```

**Příklad:**
```powershell
# Aktuální stav: InitialCreate → AddPhoneNumber → AddCourseTable

# Vrátit na AddPhoneNumber (smaže CourseTable)
Update-Database -Migration AddPhoneNumber
```

**Co se stane:**
- EF Core zavolá `Down()` metodu migrace `AddCourseTable`
- Smaže tabulku/sloupec
- Odstraní záznam z `__EFMigrationsHistory`

#### **Vrátit na úplný začátek**

```powershell
Update-Database -Migration 0
```

**Co se stane:**
- Zavolá `Down()` metody **všech** migrací
- Databáze bude **prázdná** (jen systémové tabulky)

### 7.12 Běžné chyby při migraci

#### **Chyba 1: "Build failed"**

```
Build started...
Build FAILED.
```

**Příčina:**
- Syntaktická chyba v C# kódu
- Chybějící using direktivy

**Řešení:**
- Opravte chyby v kódu
- Build solution (Ctrl + Shift + B)
- Zkuste migraci znovu

---

#### **Chyba 2: "No DbContext was found"**

```
No DbContext was found in assembly 'WPF_Aplikace_s_db'.
```

**Příčina:**
- DbContext třída není public
- DbContext není v projektu

**Řešení:**
```csharp
// Ujistěte se, že třída je public
public class StudentContext : DbContext  // ← MUSÍ být public!
```

---

#### **Chyba 3: "The migration '...' has already been applied"**

```
The migration '20260215_AddPhoneNumber' has already been applied to the database.
Remove-Migration can be used to remove the last migration.
```

**Příčina:**
- Snažíte se odstranit migraci, která již byla aplikována

**Řešení:**
```powershell
# 1. Vrátit DB na předchozí stav
Update-Database -Migration PreviousMigration

# 2. Teď můžete odstranit
Remove-Migration
```

---

#### **Chyba 4: "A network-related or instance-specific error"**

```
A network-related or instance-specific error occurred while establishing a connection to SQL Server.
```

**Příčina:**
- LocalDB není spuštěno
- Špatný connection string

**Řešení:**
```bash
# Zkontrolovat stav LocalDB
sqllocaldb info

# Spustit instanci
sqllocaldb start MSSQLLocalDB

# Zkontrolovat běžící instance
sqllocaldb info MSSQLLocalDB
```

---

#### **Chyba 5: "Cannot use EnsureCreated with migrations"**

```
The database has been created using EnsureCreated().
Migrations cannot be used when the database was created this way.
```

**Příčina:**
- Databáze byla vytvořena pomocí `EnsureCreated()`
- **EnsureCreated a migrace se vylučují!**

**Řešení:**
```powershell
# 1. Smazat databázi
Drop-Database

# 2. Vytvořit pomocí migrací
Update-Database
```

### 7.13 Pokročilé migrace - Příklady

#### **Přejmenování sloupce**

```powershell
Add-Migration RenameEmailToEmailAddress
```

**Vygenerovaná migrace:**
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.RenameColumn(
        name: "Email",
        table: "Students",
        newName: "EmailAddress");
}
```

#### **Přidání indexu**

```csharp
// V Student.cs
[Index(nameof(Email), IsUnique = true)]
public class Student
{
    // ...
}
```

```powershell
Add-Migration AddUniqueIndexOnEmail
```

**Vygenerované SQL:**
```sql
CREATE UNIQUE INDEX [IX_Students_Email] ON [Students] ([Email]);
```

#### **Změna typu sloupce**

```csharp
// Původně
public int Year { get; set; }

// Změna na
public string Year { get; set; }
```

```powershell
Add-Migration ChangeYearToString
```

**⚠️ POZOR:**
- Změna typu může způsobit **ztrátu dat**!
- EF Core varuje před nebezpečnými operacemi

#### **Přidání foreign key vztahu**

```csharp
public class Enrollment
{
    public int Id { get; set; }
    
    public int StudentId { get; set; }      // Foreign key
    public Student Student { get; set; }    // Navigation property
}
```

```powershell
Add-Migration AddEnrollmentTable
```

**Vygenerované SQL:**
```sql
CREATE TABLE [Enrollments] (
    [Id] INT NOT NULL IDENTITY(1,1),
    [StudentId] INT NOT NULL,
    CONSTRAINT [PK_Enrollments] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Enrollments_Students] FOREIGN KEY ([StudentId]) 
        REFERENCES [Students] ([Id]) ON DELETE CASCADE
);
```

### 7.14 Data seeding v migraci

#### **Přidání seed dat přímo v migraci**

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(/* ... */);
    
    // Seed data
    migrationBuilder.InsertData(
        table: "Students",
        columns: new[] { "Id", "FirstName", "LastName", "Year", "Email", "CreatedAt" },
        values: new object[,]
        {
            { 1, "Jan", "Novák", 1, "jan.novak@example.com", new DateTime(2026, 1, 1) },
            { 2, "Petr", "Svoboda", 2, "petr.svoboda@example.com", new DateTime(2026, 1, 1) }
        });
}
```

**Výhody:**
- Data jsou součástí migrace
- Automaticky se aplikují s migrací
- Verzované v Gitu

#### **HasData v OnModelCreating**

```csharp
// StudentContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>().HasData(
        new Student { Id = 1, FirstName = "Jan", LastName = "Novák", Year = 1, Email = "jan@example.com", CreatedAt = DateTime.UtcNow },
        new Student { Id = 2, FirstName = "Petr", LastName = "Svoboda", Year = 2, Email = "petr@example.com", CreatedAt = DateTime.UtcNow }
    );
}
```

**Po Add-Migration:**
- EF Core automaticky vygeneruje `InsertData` příkazy v migraci

**⚠️ DŮLEŽITÉ:**
- Musíte **ručně přiřadit Id** (není auto-generated)

### 7.15 Migrace v týmovém prostředí

#### **Workflow při spolupráci**

**Developer A:**
```powershell
# 1. Změní model (přidá sloupec)
# 2. Vytvoří migraci
Add-Migration AddPhoneNumber

# 3. Commitne do Gitu
git add .
git commit -m "Add PhoneNumber column"
git push
```

**Developer B:**
```bash
# 1. Stáhne změny
git pull

# 2. Aplikuje migrace
Update-Database
```

**Výsledek:**
- Developer B má stejnou strukturu DB jako Developer A
- Žádné manuální SQL skripty!

#### **Řešení konfliktů migrací**

**Problém:**
- Developer A vytvoří migraci `20260215_AddPhoneNumber`
- Developer B vytvoří migraci `20260215_AddAddress` (stejný čas!)
- Git merge conflict

**Řešení:**
```powershell
# 1. Odstranit lokální migraci
Remove-Migration

# 2. Stáhnout změny
git pull

# 3. Aplikovat cizí migrace
Update-Database

# 4. Vytvořit svou migraci znovu
Add-Migration AddAddress
```

### 7.16 Connection String a cesta dat - Kompletní tok

#### **Jak data putují z aplikace do .mdf souboru**

**1. Connection String v kódu:**

```csharp
// StudentContext.cs
optionsBuilder.UseSqlServer(
    "Data Source=(localdb)\\MSSQLLocalDB;" +      // ← Kam se připojit
    "Initial Catalog=StudentDbDemo1;" +            // ← Jaká databáze
    "Integrated Security=True;" +                  // ← Windows autentizace
    "TrustServerCertificate=True");                // ← Důvěřuj certifikátu
```

**2. První připojení - co se stane:**

```
Aplikace                                LocalDB Instance
   │                                           │
   │  1. Požadavek na připojení                │
   │─────────────────────────────────────────→│
   │     Connection String                     │
   │                                           │
   │                                      2. Zkontroluje, zda    
   │                                         instance běží       
   │                                           │
   │                                      3. Pokud ne, STARTUJE  
   │                                         sqlservr.exe proces 
   │                                           │
   │                                      4. Načte databázový    
   │                                         soubor .mdf         
   │                                           │
   │                                      5. Otevře connection   
   │  6. Connection úspěšné                    │
   │←─────────────────────────────────────────│
   │                                           │
```

**3. Databázový soubor - fyzická cesta:**

```
Connection String říká:
"Data Source=(localdb)\MSSQLLocalDB"
      ↓
LocalDB Instance hledá databázi:
"Initial Catalog=StudentDbDemo1"
      ↓
Databáze je v:
C:\Users\<username>\StudentDbDemo1.mdf

Pokud neexistuje, vytvoří se zde:
C:\Users\<username>\
```

**4. Zápis dat - podrobný proces:**

```
┌──────────────────────────────────────────────────────────────┐
│  APLIKACE: _db.Students.Add(new Student { ... });            │
│            _db.SaveChanges();                                │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  EF CORE CHANGE TRACKER:                                     │
│    - Detekuje změny (Added, Modified, Deleted)               │
│    - Připraví SQL příkazy                                    │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  SQL SERVER PROVIDER (Microsoft.EF.SqlServer):               │
│    - Přeloží na T-SQL                                        │
│    - Vytvoří parametrizovaný dotaz                           │
│                                                              │
│    INSERT INTO [Students] ([FirstName], [LastName], ...)     │
│    VALUES (@p0, @p1, ...);                                   │
│    SELECT [Id] FROM [Students] WHERE @@ROWCOUNT = 1          │
│      AND [Id] = SCOPE_IDENTITY();                            │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  ADO.NET (System.Data.SqlClient):                            │
│    - Otevře connection (nebo použije z poolu)                │
│    - Odešle SQL příkaz                                       │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  LOCALDB PROCES (sqlservr.exe):                              │
│    - Přijme příkaz                                           │
│    - Začne transakci                                         │
│    - Zamkne tabulku/řádek                                    │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  BUFFER POOL (RAM):                                          │
│    - Načte data page z .mdf souboru do paměti                │
│    - Provede změnu v paměti                                  │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  TRANSACTION LOG (.ldf soubor):                              │
│                                                              │
│    StudentDbDemo1_log.ldf                                    │
│    ├─ Log Record: INSERT Student (FirstName='Jan')           │
│    ├─ LSN (Log Sequence Number): 000000123                   │
│    └─ Transaction ID: TX_456                                 │
│                                                              │
│    Zápis je FIRST (Write-Ahead Logging principle)            │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  DATA FILE (.mdf soubor):                                    │
│                                                              │
│    StudentDbDemo1.mdf                                        │
│    └─ Table: dbo.Students                                    │
│       └─ Data Page 1:96 (například)                          │
│          ├─ Row 1: [1, "Jan", "Novák", ...]                  │
│          ├─ Row 2: [2, "Petr", "Svoboda", ...]               │
│          └─ Row 3: [3, "Karel", "Černý", ...]  ← NOVÝ ŘÁDEK  │
│                                                              │
│    Změněná stránka se označí jako "dirty"                    │
│    Checkpoint proces ji později zapíše na disk               │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  COMMIT TRANSAKCE:                                           │
│    - Transakce se označí jako committed v .ldf               │
│    - Odemkne zámky                                           │
│    - Vrátí vygenerované Id do aplikace                       │
└───────────────────┬──────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────────────────────┐
│  NÁVRAT DO APLIKACE:                                         │
│    - SaveChanges() vrací počet změněných záznamů (1)         │
│    - student.Id je naplněno databází (např. 11)              │
│    - Change Tracker aktualizuje stav na "Unchanged"          │
└──────────────────────────────────────────────────────────────┘
```

### 7.17 Detailní popis datových souborů

#### **.mdf soubor (Master Data File)**

**Obsah:**
```
StudentDbDemo1.mdf (8 MB po vytvoření)
│
├── File Header (první 8KB stránka)
│   ├── Database ID
│   ├── Verze SQL Serveru
│   └── Last backup informace
│
├── System Pages
│   ├── Boot Page (stránka 9)
│   ├── File Header Pages
│   └── PFS (Page Free Space) pages
│
├── System Tables (alokované SQL Serverem)
│   ├── sys.tables             # Metadata o tabulkách
│   ├── sys.columns            # Metadata o sloupcích
│   ├── sys.indexes            # Indexy
│   ├── sys.foreign_keys       # Cizí klíče
│   └── __EFMigrationsHistory  # EF Core migrace historie
│
└── User Tables
    └── dbo.Students (naše tabulka)
        ├── Data Pages (8KB každá)
        │   ├── Page Header (96 bytes)
        │   ├── Row Offsets Array
        │   └── Actual Rows (data studentů)
        │       ├── Row 1: [1|Jan|Novák|1|jan@...|2026-01-01]
        │       ├── Row 2: [2|Petr|Svoboda|2|petr@...|2026-01-01]
        │       └── ...
        │
        └── Index Pages (Clustered Index na Id)
            └── B-Tree structure pro rychlé vyhledávání
```

**Page structure (8KB stránka):**
```
┌────────────────────────────────────────────────┐
│ Page Header (96 bytes)                         │
│  - Page ID                                     │
│  - Page Type                                   │
│  - Free space                                  │
│  - Previous/Next page pointers                 │
├────────────────────────────────────────────────┤
│ Row Data (až ~8000 bytes)                      │
│  Row 1: [1|Jan|Novák|1|jan@example.com|...]    │
│  Row 2: [2|Petr|Svoboda|2|petr@example.com|...]│
│  Row 3: ...                                    │
├────────────────────────────────────────────────┤
│ Row Offset Array                               │
│  [Row3_offset, Row2_offset, Row1_offset]       │
└────────────────────────────────────────────────┘
```

#### **.ldf soubor (Log Data File)**

**Obsah:**
```
StudentDbDemo1_log.ldf (8 MB po vytvoření)
│
└── Transaction Log Records (zápisy transakcí)
    ├── Log Record 1: BEGIN TRANSACTION
    ├── Log Record 2: INSERT Students (Jan Novák)
    ├── Log Record 3: COMMIT TRANSACTION
    ├── Log Record 4: BEGIN TRANSACTION
    ├── Log Record 5: UPDATE Students SET FirstName='Petr' WHERE Id=1
    └── Log Record 6: COMMIT TRANSACTION
```

**Každý log záznam obsahuje:**
- **LSN** (Log Sequence Number) - unikátní číslo
- **Transaction ID**
- **Operace** (INSERT, UPDATE, DELETE)
- **Data** (před/po změně)
- **Timestamp**

**Účel:**
1. **Crash recovery** - Obnova po pádu
2. **Rollback** - Vrácení transakce
3. **Point-in-time restore** - Obnova k danému času
4. **Replication** - Replikace změn

### 7.18 Migrace v praxi - Kompletní příklad

#### **Scénář: Rozšíření aplikace o kurzy**

**Krok 1: Vytvořit novou entitu Course**

```csharp
// Data/Course.cs
using System.ComponentModel.DataAnnotations;

namespace WPF_Aplikace_s_db.Data
{
    public class Course
    {
        [Key]
        public int Id { get; set; }
        
        [Required]
        [StringLength(100)]
        public string Name { get; set; } = string.Empty;
        
        [StringLength(20)]
        public string Code { get; set; } = string.Empty;
        
        public int Credits { get; set; }
    }
}
```

**Krok 2: Přidat do StudentContext**

```csharp
// StudentContext.cs
public class StudentContext : DbContext
{
    public DbSet<Student> Students => Set<Student>();
    public DbSet<Course> Courses => Set<Course>();  // ← NOVÝ DbSet
    
    // ...
}
```

**Krok 3: Vytvořit migraci**

```powershell
Add-Migration CreateCoursesTable
```

**Vygenerovaná migrace:**

```csharp
public partial class CreateCoursesTable : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Courses",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:Identity", "1, 1"),
                Name = table.Column<string>(maxLength: 100, nullable: false),
                Code = table.Column<string>(maxLength: 20, nullable: false),
                Credits = table.Column<int>(nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Courses", x => x.Id);
            });
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(name: "Courses");
    }
}
```

**Krok 4: Aplikovat migraci**

```powershell
Update-Database
```

**Výstup v konzoli:**
```
Build started...
Build succeeded.
Applying migration '20260215143000_CreateCoursesTable'.
Done.
```

**Ověření v databázi:**
```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_NAME = 'Courses';
```

### 7.19 Migrace pro přechod z EnsureCreated

**Současný stav projektu:**
- Používá `Database.EnsureCreated()`
- **Nemá** migrační historii
- **Nemá** složku `Migrations/`

**Jak přejít na migrace?**

#### **Varianta A: Čistý start (DOPORUČENO pro výuku)**

**Krok 1: Smazat databázi**

```powershell
# V Package Manager Console
Drop-Database
```

Nebo v SSMS:
```sql
USE master;
DROP DATABASE StudentDbDemo1;
```

**Krok 2: Upravit MainWindow konstruktor**

```csharp
// MainWindow.xaml.cs
public MainWindow()
{
    InitializeComponent();
    
    // ZAKOMENTOVAT/SMAZAT:
    // _db.EnsureCreatedAndSeed();
    
    // PŘIDAT:
    _db.Database.Migrate();      // Aplikuje migrace
    _db.SeedIfEmpty();           // Seed zůstává
    
    // ... zbytek kódu
}
```

**Krok 3: Vytvořit první migraci**

```powershell
Add-Migration InitialCreate
```

**Krok 4: Aplikovat migraci**

```powershell
Update-Database
```

**Výsledek:**
- ✅ Databáze vytvořena pomocí migrací
- ✅ Máte složku `Migrations/`
- ✅ Historie verzování funguje

#### **Varianta B: Zachovat existující data**

**Pokud chcete zachovat data z existující DB:**

**Krok 1: Exportovat data**

```sql
-- V SSMS
SELECT * FROM Students;
-- Pravý klik na výsledky → Save Results As → students_backup.csv
```

**Krok 2: Smazat DB a vytvořit s migraci**

```powershell
Drop-Database
Add-Migration InitialCreate
Update-Database
```

**Krok 3: Importovat data zpět**

```csharp
// Jednorázový import v MainWindow konstruktoru
ImportFromCsv("students_backup.csv");
```

### 7.20 Tipy a best practices pro migrace

#### ✅ DOPORUČENÉ POSTUPY

**1. Popisné názvy migrací**
```powershell
✅ Add-Migration AddPhoneNumberToStudent
✅ Add-Migration CreateCoursesTable
✅ Add-Migration RenameEmailColumn

❌ Add-Migration Migration1
❌ Add-Migration Update
❌ Add-Migration Fix
```

**2. Časté commitování**
```bash
# Po každé migraci
git add Migrations/
git commit -m "Add migration: AddPhoneNumber"
```

**3. Review vygenerovaného kódu**
- Vždy zkontrolujte `Up()` a `Down()` metody
- Někdy EF Core generuje suboptimální SQL

**4. Testování rollbacku**
```powershell
# Aplikovat migraci
Update-Database

# Otestovat rollback
Update-Database -Migration PreviousMigration

# Aplikovat znovu
Update-Database
```

**5. Backup před aplikací migrace na produkci**
```sql
BACKUP DATABASE ProductionDb
TO DISK = 'C:\Backups\before_migration.bak';
```

#### ❌ CO NEDĚLAT

**1. Editovat již aplikované migrace**
```csharp
// NIKDY needitujte migraci, která již byla aplikována!
// Místo toho vytvořte novou migraci s opravou
```

**2. Mazat migrace z historie**
```bash
# ❌ NIKDY
git rm Migrations/20260215_InitialCreate.cs
```

**3. Kombinovat EnsureCreated a Migrate**
```csharp
// ❌ NEFUNGUJE
Database.EnsureCreated();
Database.Migrate();
```

**4. Commitovat bez testování**
```bash
# ❌ ŠPATNĚ
Add-Migration AddColumn
git commit  # Bez testování!

# ✅ SPRÁVNĚ
Add-Migration AddColumn
Update-Database  # Otestovat lokálně
# Ověřit, že funguje
git commit
```

### 📋 Shrnutí kapitoly 7
- Migrace verzují databázové schéma (jako Git pro DB)
- `Add-Migration` vytvoří migraci, `Update-Database` ji aplikuje
- EF.Core.SqlServer provider generuje T-SQL příkazy
- EF.Core.Tools poskytuje PMC příkazy (Add-Migration, atd.)
- Data putují: Aplikace → EF Core → ADO.NET → LocalDB → .mdf soubor
- LocalDB ukládá data do `C:\Users\<username>\*.mdf`
- `__EFMigrationsHistory` trackuje aplikované migrace
- EnsureCreated a Migrate se vylučují
- Pro produkci vždy používejte migrace

### 💡 Cheat Sheet - Migrační příkazy

#### **Package Manager Console (PMC)**
| Příkaz | Účel | Příklad |
|--------|------|---------|
| `Add-Migration <Name>` | Vytvoří novou migraci | `Add-Migration InitialCreate` |
| `Update-Database` | Aplikuje všechny čekající migrace | `Update-Database` |
| `Update-Database -Migration <Name>` | Aplikuje/vrátí na konkrétní migraci | `Update-Database -Migration AddPhone` |
| `Remove-Migration` | Odstraní poslední migraci | `Remove-Migration` |
| `Script-Migration` | Vygeneruje SQL skript | `Script-Migration -Output script.sql` |
| `Drop-Database` | Smaže databázi | `Drop-Database` |
| `Get-Migration` | Seznam migrací | `Get-Migration` |
| `Get-DbContext` | Seznam DbContext tříd | `Get-DbContext` |

#### **.NET CLI**
| Příkaz | Účel |
|--------|------|
| `dotnet ef migrations add <Name>` | Vytvoří migraci |
| `dotnet ef database update` | Aplikuje migrace |
| `dotnet ef migrations remove` | Odstraní poslední migraci |
| `dotnet ef migrations script` | Vygeneruje SQL skript |
| `dotnet ef database drop` | Smaže databázi |
| `dotnet ef dbcontext list` | Seznam DbContext tříd |

#### **Databázové soubory**
| Soubor | Umístění | Obsah |
|--------|----------|-------|
| `.mdf` | `C:\Users\<username>\` | Tabulky, data, indexy |
| `.ldf` | `C:\Users\<username>\` | Transaction log |

#### **SQL příkazy pro inspekci**
| Příkaz | Účel |
|--------|------|
| `SELECT * FROM __EFMigrationsHistory` | Aplikované migrace |
| `EXEC sp_help 'Students'` | Struktura tabulky |
| `sp_spaceused 'Students'` | Velikost tabulky |
| `SELECT @@VERSION` | Verze SQL Serveru |

---

## Kapitola 8: XAML a MainWindow - UI struktura

### 8.1 Co je XAML?

**XAML** (eXtensible Application Markup Language) je **značkovací jazyk** pro definici uživatelského rozhraní.

**Podobnost s HTML:**
- Oba jsou XML-based
- Hierarchická struktura (parent-child)
- Atributy pro konfiguraci

**Rozdíly:**
- XAML vytváří .NET objekty
- HTML vytváří DOM elementy

### 8.2 Struktura MainWindow.xaml

#### **Root element - Window**

```xml
<Window x:Class="WPF_Aplikace_s_db.MainWindow" 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" 
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
        xmlns:local="clr-namespace:WPF_Aplikace_s_db"
        xmlns:sys="clr-namespace:System;assembly=System.Runtime"
        mc:Ignorable="d" 
        Title="MainWindow" 
        Height="450" 
        Width="800">
```

### 8.3 XML Namespaces - Detailní vysvětlení

#### **x:Class** - Propojení s C# třídou

```xml
x:Class="WPF_Aplikace_s_db.MainWindow"
```

**Co to znamená:**
- Spojuje XAML soubor s C# třídou `MainWindow`
- Umožňuje použít code-behind (MainWindow.xaml.cs)
- **Musí odpovídat** namespace a názvu třídy v `.cs` souboru

**V MainWindow.xaml.cs:**
```csharp
namespace WPF_Aplikace_s_db
{
    public partial class MainWindow : Window  // <-- "partial" klíčové!
    {
        // ...
    }
}
```

**Partial class:**
- XAML generuje druhou část třídy (InitializeComponent)
- Obě části se sloučí při kompilaci

#### **xmlns** - Default namespace

```xml
xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
```

**Co to znamená:**
- **Default** XML namespace pro WPF elementy
- Obsahuje všechny základní WPF kontrolky: `Button`, `TextBox`, `Grid`, atd.
- Nepotřebujete prefix: `<Button>` (ne `<wpf:Button>`)

**Elementy z tohoto namespace:**
- `<Window>`, `<Grid>`, `<Button>`, `<TextBox>`, `<DataGrid>`, atd.

#### **xmlns:x** - XAML namespace

```xml
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
```

**Co to znamená:**
- Obsahuje XAML **direktivy** a **klíčová slova**
- Prefix: `x:`

**Běžné direktivy:**
- `x:Class` - propojení s C# třídou
- `x:Name` - pojmenování elementu (přístup z C#)
- `x:Key` - klíč pro resource
- `x:Type` - reference na .NET typ
- `x:Array` - vytvoření pole

**Příklad použití:**
```xml
<Button x:Name="BtnSave" />  <!-- Můžeme použít v C#: BtnSave.Click += ... -->
```

#### **xmlns:d** - Design namespace

```xml
xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
```

**Co to znamená:**
- Obsahuje **design-time** atributy
- Používá se pouze v **XAML designeru**
- **Neovlivňuje runtime** aplikace

**Příklady:**
```xml
<TextBlock d:Text="Preview text" Text="{Binding RealData}" />
<ListView d:ItemsSource="{d:SampleData ItemCount=5}" />
```

#### **xmlns:mc** - Markup Compatibility

```xml
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
```

**Co to znamená:**
- Řídí kompatibilitu různých verzí XAML
- Často používaný s `mc:Ignorable`

**mc:Ignorable="d"**
```xml
mc:Ignorable="d"
```

- Ignoruj namespace `d:` za runtime
- Design atributy se nebudou načítat v běžící aplikaci

#### **xmlns:local** - Lokální namespace

```xml
xmlns:local="clr-namespace:WPF_Aplikace_s_db"
```

**Co to znamená:**
- Reference na **váš vlastní** namespace
- Můžete použít vaše vlastní třídy v XAML

**Příklad použití:**
```xml
<!-- Předpokládejme třídu StudentViewModel v namespace WPF_Aplikace_s_db -->
<local:StudentViewModel />

<!-- Použití vlastní custom kontrolky -->
<local:MyCustomControl />
```

#### **xmlns:sys** - System namespace

```xml
xmlns:sys="clr-namespace:System;assembly=System.Runtime"
```

**Co to znamená:**
- Reference na .NET **System** namespace
- `assembly=System.Runtime` - v jakém assembly se nachází

**Použití v našem projektu:**
```xml
<x:Array Type="{x:Type sys:Int32}">
    <sys:Int32>1</sys:Int32>
    <sys:Int32>2</sys:Int32>
    <sys:Int32>3</sys:Int32>
</x:Array>
```

- Vytváří pole integerů (1, 2, 3) pro ComboBox

### 8.4 Window atributy

```xml
Title="MainWindow" 
Height="450" 
Width="800"
```

| Atribut | Význam |
|---------|--------|
| `Title` | Titulek okna (v záhlaví) |
| `Height` | Výška okna v pixelech |
| `Width` | Šířka okna v pixelech |

**Další užitečné atributy:**
```xml
WindowState="Maximized"           <!-- Spustit maximalizované -->
WindowStartupLocation="CenterScreen"  <!-- Vycentrovat na obrazovce -->
ResizeMode="CanMinimize"          <!-- Povolit pouze minimalizaci -->
Icon="app_icon.ico"               <!-- Ikona aplikace -->
```

### 8.5 Layout - Grid

```xml
<Grid Margin="12">
    <Grid.RowDefinitions>
        <RowDefinition Height="2*"/>
        <RowDefinition Height="6"/>
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    <!-- ... -->
</Grid>
```

#### **Co je Grid?**

`Grid` je **layout panel**, který:
- Rozděluje prostor na **řádky** (rows) a **sloupce** (columns)
- Podobný HTML `<table>` (ale flexibilnější)

#### **RowDefinitions**

```xml
<Grid.RowDefinitions>
    <RowDefinition Height="2*"/>   <!-- Row 0: 2 části prostoru -->
    <RowDefinition Height="6"/>    <!-- Row 1: Fixní 6 pixelů -->
    <RowDefinition Height="Auto"/> <!-- Row 2: Podle obsahu -->
</Grid.RowDefinitions>
```

**Typy výšky:**

| Syntaxe | Význam | Příklad |
|---------|--------|---------|
| `*` (star) | Proporcionální | `Height="*"` - 1 část |
| `2*`, `3*` | Vícenásobek | `Height="2*"` - 2 části |
| `100` | Fixní pixely | `Height="100"` |
| `Auto` | Podle obsahu | `Height="Auto"` |

**Příklad:**
```xml
<RowDefinition Height="2*"/>   <!-- 2/3 prostoru -->
<RowDefinition Height="*"/>    <!-- 1/3 prostoru -->
```

Pokud máme výšku okna 450px:
- Row 0: `450 * (2/3) = 300px`
- Row 1: `450 * (1/3) = 150px`

#### **Umístění elementů do Grid**

```xml
<DataGrid Grid.Row="0" />          <!-- První řádek -->
<GridSplitter Grid.Row="1" />      <!-- Druhý řádek -->
<Grid Grid.Row="2" />              <!-- Třetí řádek -->
```

### 8.6 DataGrid - Zobrazení dat

```xml
<DataGrid x:Name="StudentsGrid" 
          Grid.Row="0" 
          AutoGenerateColumns="False" 
          CanUserAddRows="False" 
          IsReadOnly="False">
```

#### **DataGrid atributy**

| Atribut | Význam |
|---------|--------|
| `x:Name="StudentsGrid"` | Jméno pro přístup z C# |
| `AutoGenerateColumns="False"` | Nechceme automatické sloupce |
| `CanUserAddRows="False"` | Zakázat přidávání řádků v gridu |
| `IsReadOnly="False"` | Povolit editaci buněk |

#### **DataGridTextColumn - Textový sloupec**

```xml
<DataGridTextColumn Header="Jméno" 
                    Binding="{Binding FirstName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" 
                    Width="*"/>
```

**Atributy:**
- `Header` - Hlavička sloupce
- `Binding` - Propojení s property (viz další sekce)
- `Width="*"` - Proporcionální šířka

### 8.7 Data Binding - Detailní vysvětlení

```xml
Binding="{Binding FirstName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"
```

#### **Co je Data Binding?**

**Data binding** = Automatické **propojení UI a dat**.

**Bez bindingu:**
```csharp
// Musíte ručně synchronizovat
TextBox1.Text = student.FirstName;
student.FirstName = TextBox1.Text;
```

**S bindingem:**
```xml
<TextBox Text="{Binding FirstName, Mode=TwoWay}" />
```
- Automatická synchronizace
- UI se aktualizuje při změně dat
- Data se aktualizují při změně UI

#### **Binding Modes**

| Mode | Směr | Použití |
|------|------|---------|
| `OneWay` | Data → UI | Read-only zobrazení |
| `TwoWay` | Data ↔ UI | Editovatelná pole |
| `OneTime` | Data → UI (jednou) | Statická data |
| `OneWayToSource` | UI → Data | Inverzní binding |

**Příklady:**
```xml
<!-- Read-only label -->
<TextBlock Text="{Binding CreatedAt, Mode=OneWay}" />

<!-- Editovatelný TextBox -->
<TextBox Text="{Binding FirstName, Mode=TwoWay}" />
```

#### **UpdateSourceTrigger**

Určuje, **kdy** se aktualizují zdrojová data.

| Hodnota | Kdy se aktualizuje | Použití |
|---------|-------------------|---------|
| `PropertyChanged` | Při každé změně znaku | Real-time aktualizace |
| `LostFocus` | Při opuštění pole | Default pro TextBox |
| `Explicit` | Ručně (volání `UpdateSource()`) | Speciální případy |

**Příklad:**
```xml
<!-- Aktualizace při každém stisknutí klávesy -->
<TextBox Text="{Binding FirstName, UpdateSourceTrigger=PropertyChanged}" />

<!-- Aktualizace při opuštění pole (výchozí) -->
<TextBox Text="{Binding FirstName}" />
```

### 8.8 DataGridComboBoxColumn

```xml
<DataGridComboBoxColumn Header="Ročník" 
                        Width="90"
                        SelectedItemBinding="{Binding Year, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}">
    <DataGridComboBoxColumn.ItemsSource>
        <x:Array Type="{x:Type sys:Int32}">
            <sys:Int32>1</sys:Int32>
            <sys:Int32>2</sys:Int32>
            <sys:Int32>3</sys:Int32>
            <sys:Int32>4</sys:Int32>
            <sys:Int32>5</sys:Int32>
            <sys:Int32>6</sys:Int32>
        </x:Array>
    </DataGridComboBoxColumn.ItemsSource>
</DataGridComboBoxColumn>
```

#### **SelectedItemBinding**

```xml
SelectedItemBinding="{Binding Year, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"
```

- Binduje na property `Year` studenta
- `TwoWay` - změna v ComboBoxu aktualizuje `Year`

#### **ItemsSource - Vytvoření pole**

```xml
<x:Array Type="{x:Type sys:Int32}">
    <sys:Int32>1</sys:Int32>
    <!-- ... -->
</x:Array>
```

**Co to dělá:**
- Vytváří pole integerů v XAML
- `{x:Type sys:Int32}` - typ pole je `int[]`
- `<sys:Int32>1</sys:Int32>` - jednotlivé hodnoty

**Alternativa v C#:**
```csharp
var years = new int[] { 1, 2, 3, 4, 5, 6 };
```

### 8.9 StringFormat - Formátování textu

```xml
<DataGridTextColumn Header="Vytvořeno" 
                    Binding="{Binding CreatedAt, StringFormat={}{0:yyyy-MM-dd HH:mm:ss}}" 
                    Width="180"/>
```

#### **StringFormat syntaxe**

```xml
StringFormat={}{0:yyyy-MM-dd HH:mm:ss}
```

**Vysvětlení:**
- `{}` - Escape sekvence (XAML si myslí, že `{` začíná markup extension)
- `{0:...}` - Composite format string (.NET)
- `yyyy-MM-dd HH:mm:ss` - Format pattern

**Příklady formátů:**

| Pattern | Výstup (pro 2026-02-15 14:30:45) |
|---------|----------------------------------|
| `yyyy-MM-dd` | 2026-02-15 |
| `dd.MM.yyyy` | 15.02.2026 |
| `HH:mm:ss` | 14:30:45 |
| `yyyy-MM-dd HH:mm` | 2026-02-15 14:30 |

**Formátování čísel:**
```xml
<!-- Měna -->
StringFormat={}{0:C}          <!-- $1,234.56 -->

<!-- Procenta -->
StringFormat={}{0:P}          <!-- 12.34% -->

<!-- Dvě desetinná místa -->
StringFormat={}{0:F2}         <!-- 123.45 -->
```

### 8.10 GridSplitter - Změna velikosti

```xml
<GridSplitter Grid.Row="1" 
              Height="6" 
              HorizontalAlignment="Stretch" 
              VerticalAlignment="Center" 
              Background="#DDD"/>
```

**Co dělá:**
- Umožňuje uživateli **měnit velikost** řádků/sloupců
- Táhnutím myší můžete zvětšit/zmenšit DataGrid

### 📋 Shrnutí kapitoly 8
- XAML je značkovací jazyk pro UI definici
- `xmlns` definuje XML namespaces
- `x:Class` propojuje XAML s C# třídou
- Grid rozděluje prostor na řádky a sloupce
- Data binding automaticky synchronizuje UI a data
- `Mode=TwoWay` umožňuje obousměrnou synchronizaci
- `UpdateSourceTrigger` určuje, kdy se aktualizují data

### 💡 Cheat Sheet - XAML Binding
| Syntaxe | Význam |
|---------|--------|
| `{Binding PropertyName}` | Základní binding |
| `Mode=TwoWay` | Obousměrná synchronizace |
| `Mode=OneWay` | Pouze z dat do UI |
| `UpdateSourceTrigger=PropertyChanged` | Aktualizace při každé změně |
| `StringFormat={}{0:...}` | Formátování hodnoty |
| `{Binding Path=.}` | Binding na celý objekt |

---

## Kapitola 9: Code-behind - MainWindow.xaml.cs

### 9.1 Struktura MainWindow.xaml.cs

```csharp
namespace WPF_Aplikace_s_db
{
    public partial class MainWindow : Window
    {
        private readonly StudentContext _db = new StudentContext();
        private readonly ObservableCollection<Student> _students = new ObservableCollection<Student>();
        private ICollectionView _studentsView;
        
        // Konstruktor, metody...
    }
}
```

### 9.2 Fieldy (proměnné třídy)

#### **StudentContext _db**

```csharp
private readonly StudentContext _db = new StudentContext();
```

**Vysvětlení:**
- `private` - přístupné pouze v této třídě
- `readonly` - hodnota se může nastavit pouze v konstruktoru
- `_db` - konvence pojmenování (prefix `_` pro private fieldy)

**Životnost:**
- Vytvoří se při konstrukci okna
- Existuje po celou dobu života okna
- Dispose v `OnClosed()` (viz níže)

#### **ObservableCollection<Student>**

```csharp
private readonly ObservableCollection<Student> _students = new ObservableCollection<Student>();
```

**Co je ObservableCollection?**

`ObservableCollection<T>` je speciální kolekce, která:
- Automaticky **notifikuje UI** o změnách (přidání/odebrání prvků)
- Implementuje `INotifyCollectionChanged`
- **Klíčová pro data binding** s kolekcemi

**Rozdíl oproti List<T>:**

| Feature | List<T> | ObservableCollection<T> |
|---------|---------|-------------------------|
| Notifikace změn | ❌ Ne | ✅ Ano |
| Data binding | ❌ Nefunguje dynamicky | ✅ Funguje perfektně |
| Performance | ⚡ Rychlejší | 🐌 Mírně pomalejší |

**Příklad:**
```csharp
// List - UI se NEAKTUALIZUJE
List<Student> students = new List<Student>();
DataGrid.ItemsSource = students;
students.Add(new Student());  // UI se nezmění!

// ObservableCollection - UI se AKTUALIZUJE
ObservableCollection<Student> students = new ObservableCollection<Student>();
DataGrid.ItemsSource = students;
students.Add(new Student());  // UI se automaticky aktualizuje!
```

#### **ICollectionView _studentsView**

```csharp
private ICollectionView _studentsView;
```

**Co je ICollectionView?**

`ICollectionView` je **wrapper** kolem kolekce, který poskytuje:
- **Řazení** (sorting)
- **Filtrování** (filtering)
- **Seskupování** (grouping)
- **Navigaci** (current item)

**Použití v našem projektu:**
```csharp
_studentsView = CollectionViewSource.GetDefaultView(_students);
_studentsView.SortDescriptions.Add(new SortDescription(nameof(Student.Id), ListSortDirection.Ascending));
```

- Řadí studenty podle `Id` vzestupně

### 9.3 Konstruktor MainWindow

```csharp
public MainWindow()
{
    InitializeComponent();

    _db.EnsureCreatedAndSeed();

    foreach (var s in _db.Students.OrderBy(x => x.Id).ToList())
        _students.Add(s);

    _studentsView = CollectionViewSource.GetDefaultView(_students);
    _studentsView.SortDescriptions.Clear();
    _studentsView.SortDescriptions.Add(new SortDescription(nameof(Student.Id), ListSortDirection.Ascending));

    StudentsGrid.ItemsSource = _studentsView;
}
```

#### **Krok 1: InitializeComponent()**

```csharp
InitializeComponent();
```

**Co to dělá:**
- **Načte XAML** a vytvoří UI elementy
- Vygenerováno automaticky z XAML souboru
- **MUSÍ být první** v konstruktoru (před použitím UI elementů)

#### **Krok 2: Vytvoření/seedování databáze**

```csharp
_db.EnsureCreatedAndSeed();
```

- Vytvoří databázi, pokud neexistuje
- Naplní testovacími daty, pokud je prázdná

#### **Krok 3: Načtení dat z databáze**

```csharp
foreach (var s in _db.Students.OrderBy(x => x.Id).ToList())
    _students.Add(s);
```

**Rozbor:**
```csharp
_db.Students                      // DbSet<Student>
    .OrderBy(x => x.Id)           // LINQ - řazení podle Id
    .ToList()                     // Provede dotaz, vrátí List<Student>
```

**Proč ToList()?**
- `DbSet<Student>` je **IQueryable** (lazy)
- `ToList()` **provede dotaz** na databázi ihned
- Vrací `List<Student>` (načteno do paměti)

**SQL ekvivalent:**
```sql
SELECT * FROM Students ORDER BY Id
```

**Přidání do ObservableCollection:**
```csharp
foreach (var s in ...)
    _students.Add(s);
```

- Každého studenta přidáme do `_students`
- `ObservableCollection` notifikuje UI o změnách

#### **Krok 4: Vytvoření ICollectionView**

```csharp
_studentsView = CollectionViewSource.GetDefaultView(_students);
```

- Vytvoří view nad kolekcí `_students`

#### **Krok 5: Konfigurace řazení**

```csharp
_studentsView.SortDescriptions.Clear();
_studentsView.SortDescriptions.Add(
    new SortDescription(nameof(Student.Id), ListSortDirection.Ascending)
);
```

**SortDescription:**
- `nameof(Student.Id)` - řadit podle property `Id`
- `ListSortDirection.Ascending` - vzestupně (1, 2, 3, ...)

**Alternativa - sestupně:**
```csharp
ListSortDirection.Descending  // (3, 2, 1, ...)
```

#### **Krok 6: Nastavení ItemsSource**

```csharp
StudentsGrid.ItemsSource = _studentsView;
```

- Propojí DataGrid s kolekcí studentů
- DataGrid zobrazí všechny studenty

### 9.4 OnClosed - Dispose DbContext

```csharp
protected override void OnClosed(EventArgs e)
{
    _db.Dispose();
    base.OnClosed(e);
}
```

**Proč je to důležité?**

`DbContext` drží:
- **Databázové připojení**
- **Změny v paměti** (change tracking)
- **Další zdroje**

**Bez Dispose:**
- Connection leak (nevypnou se připojení)
- Memory leak (paměť se neuvolní)

**Pattern:**
```csharp
protected override void OnClosed(EventArgs e)
{
    _db.Dispose();       // Uvolni zdroje
    base.OnClosed(e);    // Zavolej base implementaci
}
```

### 9.5 BtnAddStudent_Click - Přidání studenta

```csharp
private void BtnAddStudent_Click(object sender, RoutedEventArgs e)
{
    string firstName = (TxtFirstName.Text ?? string.Empty).Trim();
    string lastName = (TxtLastName.Text ?? string.Empty).Trim();
    string email = (TxtEmail.Text ?? string.Empty).Trim();
    int year = 1;
    int.TryParse((TxtYear.Text ?? string.Empty).Trim(), out year);

    var s = new Student
    {
        FirstName = firstName,
        LastName = lastName,
        Year = year,
        Email = email,
        CreatedAt = DateTime.UtcNow
    };

    _db.Students.Add(s);
    _db.SaveChanges();

    _students.Add(s);
    StudentsGrid.SelectedItem = s;
    StudentsGrid.ScrollIntoView(s);

    TxtFirstName.Text = TxtLastName.Text = TxtEmail.Text = TxtYear.Text = string.Empty;
}
```

#### **Krok 1: Získání hodnot z TextBoxů**

```csharp
string firstName = (TxtFirstName.Text ?? string.Empty).Trim();
```

**Rozbor:**
- `TxtFirstName.Text` - hodnota z TextBoxu
- `?? string.Empty` - pokud je `null`, použij prázdný řetězec
- `.Trim()` - odstraň mezery na začátku/konci

**Proč `?? string.Empty`?**
- `TextBox.Text` může být `null`
- Zabráníme `NullReferenceException` při volání `.Trim()`

#### **Krok 2: Parsování Year**

```csharp
int year = 1;
int.TryParse((TxtYear.Text ?? string.Empty).Trim(), out year);
```

**int.TryParse:**
- Pokusí se převést string na int
- Pokud **selže**, `year` zůstane `1` (výchozí hodnota)
- Pokud **uspěje**, `year` bude obsahovat číslo

**Příklady:**
```csharp
int.TryParse("3", out year);     // year = 3, vrací true
int.TryParse("abc", out year);   // year = 1 (nezměněno), vrací false
int.TryParse("", out year);      // year = 1 (nezměněno), vrací false
```

#### **Krok 3: Vytvoření nového studenta**

```csharp
var s = new Student
{
    FirstName = firstName,
    LastName = lastName,
    Year = year,
    Email = email,
    CreatedAt = DateTime.UtcNow
};
```

- Object initializer syntax
- `Id` není nastaveno (auto-generuje databáze)

#### **Krok 4: Přidání do databáze**

```csharp
_db.Students.Add(s);
_db.SaveChanges();
```

**Co se stane:**
1. `Add(s)` - Označí studenta jako "přidaný" (tracked změny)
2. `SaveChanges()` - Vygeneruje SQL INSERT a provede ho

**SQL ekvivalent:**
```sql
INSERT INTO Students (FirstName, LastName, Year, Email, CreatedAt)
VALUES ('Jan', 'Novák', 1, 'jan@example.com', '2026-02-15 14:30:00')
```

**Po SaveChanges:**
- `s.Id` je **automaticky nastaveno** databází (Identity)

#### **Krok 5: Aktualizace UI**

```csharp
_students.Add(s);
StudentsGrid.SelectedItem = s;
StudentsGrid.ScrollIntoView(s);
```

**Proč přidat i do _students?**
- `_db.Students` je databázová kolekce
- `_students` je UI kolekce (ObservableCollection)
- Musíme synchronizovat obě

**SelectedItem:**
- Označí nově přidaného studenta jako vybraného

**ScrollIntoView:**
- Posune DataGrid, aby nový student byl viditelný

#### **Krok 6: Vyčištění formuláře**

```csharp
TxtFirstName.Text = TxtLastName.Text = TxtEmail.Text = TxtYear.Text = string.Empty;
```

- Vymaže všechny TextBoxy
- Připraveno pro přidání dalšího studenta

### 9.6 BtnSave_Click - Uložení změn

```csharp
private void BtnSave_Click(object sender, RoutedEventArgs e)
{
    var selected = StudentsGrid.SelectedItem as Student;
    if (selected != null)
    {
        var result = MessageBox.Show(this,
            $"Opravdu chcete údaje studenta {selected.FirstName} {selected.LastName}?",
            "Uložit?",
            MessageBoxButton.YesNo,
            MessageBoxImage.Question);
        if (result == MessageBoxResult.Yes)
        {
            _db.SaveChanges();
        }
    }
}
```

#### **SelectedItem as Student**

```csharp
var selected = StudentsGrid.SelectedItem as Student;
```

**Co to dělá:**
- `SelectedItem` je `object` (může být cokoli)
- `as Student` - safe cast (pokud selže, vrací `null`)

**Alternativa - unsafe cast:**
```csharp
var selected = (Student)StudentsGrid.SelectedItem;  // Může hodit InvalidCastException
```

#### **MessageBox.Show**

```csharp
MessageBox.Show(this,
    $"Opravdu chcete údaje studenta {selected.FirstName} {selected.LastName}?",
    "Uložit?",
    MessageBoxButton.YesNo,
    MessageBoxImage.Question);
```

**Parametry:**
- `this` - owner window (okno, které otevírá dialog)
- Message text (s string interpolation `$"..."`)
- Titulek dialogu
- `MessageBoxButton.YesNo` - tlačítka Ano/Ne
- `MessageBoxImage.Question` - ikona otazníku

**Možné hodnoty MessageBoxButton:**
- `OK`
- `OKCancel`
- `YesNo`
- `YesNoCancel`

**Možné hodnoty MessageBoxImage:**
- `None`, `Information`, `Question`, `Warning`, `Error`

#### **SaveChanges - Jak funguje?**

```csharp
_db.SaveChanges();
```

**Entity Framework Change Tracking:**

1. DataGrid je bindovaný na `_students`
2. `_students` obsahuje objekty z `_db.Students`
3. EF Core **trackuje změny** těchto objektů
4. Když editujete v DataGridu, změní se property objektu
5. `SaveChanges()` detekuje změny a vygeneruje SQL UPDATE

**Příklad:**
```csharp
var student = _db.Students.First();
student.FirstName = "Petr";    // EF Core si pamatuje původní hodnotu ("Jan")
_db.SaveChanges();              // Vygeneruje: UPDATE Students SET FirstName='Petr' WHERE Id=1
```

### 9.7 BtnDeleteSelected_Click - Smazání studenta

```csharp
private void BtnDeleteSelected_Click(object sender, RoutedEventArgs e)
{
    var selected = StudentsGrid.SelectedItem as Student;
    if (selected != null)
    {
        var result = MessageBox.Show(this,
            $"Opravdu smazat studenta {selected.FirstName} {selected.LastName}?",
            "Smazat studenta",
            MessageBoxButton.YesNo,
            MessageBoxImage.Question);
        if (result == MessageBoxResult.Yes)
        {
            _db.Students.Remove(selected);
            _db.SaveChanges();
            _students.Remove(selected);
        }
    }
}
```

#### **Remove - Smazání záznamu**

```csharp
_db.Students.Remove(selected);
_db.SaveChanges();
```

**Co se stane:**
1. `Remove(selected)` - Označí záznam jako "smazaný"
2. `SaveChanges()` - Vygeneruje SQL DELETE

**SQL ekvivalent:**
```sql
DELETE FROM Students WHERE Id = 5
```

#### **Aktualizace UI kolekce**

```csharp
_students.Remove(selected);
```

- Odstraní studenta z `ObservableCollection`
- DataGrid se automaticky aktualizuje (jeden řádek zmizí)

### 📋 Shrnutí kapitoly 9
- `ObservableCollection` automaticky notifikuje UI o změnách
- `ICollectionView` poskytuje řazení, filtrování a grouping
- `InitializeComponent()` načte XAML (musí být první v konstruktoru)
- `SaveChanges()` ukládá změny do databáze
- `Dispose()` uvolňuje zdroje (důležité pro DbContext)
- MessageBox slouží k potvrzovacím dialogům

### 💡 Cheat Sheet - DbContext operace
| Operace | Metoda | SQL ekvivalent |
|---------|--------|----------------|
| **Create** | `Add()` + `SaveChanges()` | `INSERT INTO` |
| **Read** | `ToList()`, `FirstOrDefault()`, atd. | `SELECT` |
| **Update** | Změna property + `SaveChanges()` | `UPDATE` |
| **Delete** | `Remove()` + `SaveChanges()` | `DELETE FROM` |
| **Načtení** | `DbSet.ToList()` | `SELECT * FROM` |
| **Filtrování** | `.Where(x => ...)` | `WHERE` |
| **Řazení** | `.OrderBy(x => ...)` | `ORDER BY` |

---

## Kapitola 10: Spuštění a testování aplikace

### 10.1 Build a spuštění

#### **Build projektu**

1. **Pomocí menu:**
   - Menu: **Build → Build Solution** (Ctrl + Shift + B)

2. **Pomocí klávesnice:**
   - Stiskněte **Ctrl + Shift + B**

**Co se stane:**
- Kompilace C# kódu
- Kompilace XAML souborů
- PropertyChanged.Fody weaving (INotifyPropertyChanged)
- Výstup: `bin\Debug\net10.0-windows\WPF_Aplikace_s_db.exe`

#### **Spuštění aplikace**

1. **S debuggerem (F5):**
   - Umožňuje breakpointy, step debugging
   - Pomalejší start

2. **Bez debuggeru (Ctrl + F5):**
   - Rychlejší start
   - Nelze debugovat

### 10.2 První spuštění - Co se stane?

#### **Krok 1: Vytvoření databáze**

```csharp
_db.EnsureCreatedAndSeed();
```

**Co se děje:**
1. EF Core se připojí k `(localdb)\MSSQLLocalDB`
2. Zkontroluje, zda databáze `StudentDbDemo1` existuje
3. Pokud **neexistuje**, vytvoří ji
4. Vytvoří tabulku `Students` podle entity

**Databázové schéma:**
```sql
CREATE TABLE [Students] (
    [Id] INT NOT NULL IDENTITY(1,1) PRIMARY KEY,
    [FirstName] NVARCHAR(30) NOT NULL,
    [LastName] NVARCHAR(30) NOT NULL,
    [Year] INT NOT NULL,
    [Email] NVARCHAR(50) NOT NULL,
    [CreatedAt] DATETIME2 NOT NULL
)
```

#### **Krok 2: Seedování dat**

```csharp
SeedIfEmpty();
```

- Přidá 10 testovacích studentů
- Pouze pokud tabulka je prázdná

#### **Krok 3: Načtení a zobrazení dat**

- Načte všechny studenty z databáze
- Zobrazí je v DataGridu

### 10.3 Testovací scénáře

#### **Test 1: Zobrazení dat**

✅ **Očekávaný výsledek:**
- DataGrid zobrazuje 10 studentů
- Sloupce: ID, Jméno, Příjmení, Ročník, E-mail, Vytvořeno

**Ověření:**
- Jsou viditelné všechny sloupce?
- Data jsou správně zarovnaná?
- Datum je ve formátu `yyyy-MM-dd HH:mm:ss`?

#### **Test 2: Přidání nového studenta**

**Kroky:**
1. Do pole "Jméno" zadejte: `Martin`
2. Do pole "Příjmení" zadejte: `Procházka`
3. V "Ročník" vyberte: `2`
4. Do "E-mail" zadejte: `martin.prochazka@example.com`
5. Klikněte "Přidat studenta"

✅ **Očekávaný výsledek:**
- Nový student se objeví v DataGridu
- Student je automaticky vybrán (highlighted)
- DataGrid scrolluje na nového studenta
- Formulář se vyčistí

**Ověření v databázi:**
```sql
SELECT * FROM Students WHERE FirstName = 'Martin'
```

#### **Test 3: Editace studenta v DataGridu**

**Kroky:**
1. Klikněte na buňku "Jméno" u studenta
2. Změňte hodnotu na `Petra`
3. Klikněte na buňku "Příjmení"
4. Změňte hodnotu na `Nováková`
5. Vyberte studenta (klikněte na řádek)
6. Klikněte "💾 Uložit data"
7. Potvrďte dialog "Ano"

✅ **Očekávaný výsledek:**
- Dialog se zobrazí s novými hodnotami
- Po potvrzení se změní uloží do databáze
- DataGrid zobrazuje nové hodnoty

**Ověření:**
- Zavřete aplikaci
- Spusťte znovu
- Student má stále nové hodnoty (data jsou perzistentní)

#### **Test 4: Změna ročníku pomocí ComboBoxu**

**Kroky:**
1. Klikněte na buňku "Ročník" u studenta
2. Otevře se ComboBox s hodnotami 1-6
3. Vyberte jinou hodnotu (např. 4)
4. Vyberte studenta
5. Klikněte "💾 Uložit data"

✅ **Očekávaný výsledek:**
- Ročník se změní
- Po restartu aplikace je hodnota zachována

#### **Test 5: Smazání studenta**

**Kroky:**
1. Vyberte studenta kliknutím na řádek
2. Klikněte "Smazat vybraného"
3. Potvrďte dialog "Ano"

✅ **Očekávaný výsledek:**
- Student zmizí z DataGridu
- Dialog zobrazuje správné jméno a příjmení
- Počet záznamů se sníží o 1

**Ověření:**
- Restart aplikace - student je pryč trvale

#### **Test 6: Validace (Range attribute)**

**Kroky:**
1. Přidejte studenta s ročníkem 7 (mimo rozsah 1-6)

✅ **Očekávaný výsledek:**
- V této implementaci se **neuloží validační chyba**
- Data Annotations se **nevynutí** automaticky v UI
- Pro vynucení validace je potřeba implementovat `IDataErrorInfo` nebo FluentValidation

**Poznámka:**
- V produkční aplikaci byste měli přidat **UI validaci**

### 10.4 Debugování

#### **Breakpointy**

**Jak nastavit breakpoint:**
1. Klikněte na levý okraj editoru (šedá lišta)
2. Objeví se červená tečka
3. Spusťte aplikaci s F5
4. Při dosažení breakpointu se aplikace zastaví

**Kde nastavit breakpointy:**
```csharp
// V BtnAddStudent_Click
private void BtnAddStudent_Click(object sender, RoutedEventArgs e)
{
    string firstName = ...;  // <-- BREAKPOINT TADY
    // ...
}
```

**Užitečné zkratky:**
- **F5** - Continue (pokračovat)
- **F10** - Step Over (další řádek)
- **F11** - Step Into (vstoupit do metody)
- **Shift + F11** - Step Out (vystoupit z metody)

#### **Immediate Window**

**Otevření:**
- Menu: **Debug → Windows → Immediate**
- Nebo: **Ctrl + Alt + I**

**Použití:**
```csharp
// Vyhodnotit výraz
? firstName
// Výstup: "Jan"

// Změnit hodnotu
firstName = "Test"

// Zavolat metodu
? _db.Students.Count()
// Výstup: 10
```

#### **Watch Window**

**Přidání watch:**
1. Během debugování klikněte pravým tlačítkem na proměnnou
2. Vyberte "Add Watch"
3. Proměnná se zobrazí v Watch okně

**Nebo:**
1. Otevřete Watch okno: **Debug → Windows → Watch → Watch 1**
2. Zadejte výraz (např. `_students.Count`)

### 10.5 SQL Server Management Studio (SSMS) - Prohlížení DB

#### **Připojení k LocalDB**

1. Otevřete **SQL Server Management Studio** (SSMS)
2. V dialogu připojení zadejte:
   - **Server name:** `(localdb)\MSSQLLocalDB`
   - **Authentication:** Windows Authentication
3. Klikněte **Connect**

#### **Prohlížení tabulky Students**

1. Rozbalte **Databases → StudentDbDemo1 → Tables**
2. Najděte tabulku `dbo.Students`
3. Pravý klik → **Select Top 1000 Rows**

**Výsledek:**
```sql
SELECT TOP (1000) [Id],[FirstName],[LastName],[Year],[Email],[CreatedAt]
FROM [StudentDbDemo1].[dbo].[Students]
```

#### **Užitečné SQL dotazy**

**Počet studentů:**
```sql
SELECT COUNT(*) FROM Students
```

**Studenti v ročníku 1:**
```sql
SELECT * FROM Students WHERE Year = 1
```

**Seřazení podle příjmení:**
```sql
SELECT * FROM Students ORDER BY LastName
```

**Studenti vytvořeni dnes:**
```sql
SELECT * FROM Students 
WHERE CAST(CreatedAt AS DATE) = CAST(GETDATE() AS DATE)
```

### 10.6 Běžné chyby a řešení

#### **Chyba: "A network-related or instance-specific error occurred"**

**Příčina:**
- LocalDB není nainstalováno nebo není spuštěno

**Řešení:**
```bash
# Zkontrolovat verzi LocalDB
sqllocaldb info

# Spustit instanci
sqllocaldb start MSSQLLocalDB
```

#### **Chyba: "The type initializer for 'StudentContext' threw an exception"**

**Příčina:**
- Chyba v connection stringu
- Nesprávný assembly reference

**Řešení:**
- Zkontrolujte connection string v `OnConfiguring`
- Ověřte, že jsou nainstalovány NuGet balíčky

#### **DataGrid je prázdný**

**Možné příčiny:**
1. `ItemsSource` není nastaveno
2. Binding je špatný
3. Data nebyla načtena z databáze

**Řešení:**
```csharp
// Debugujte v konstruktoru
var count = _db.Students.Count();  // Kolik je v DB?
var viewCount = _students.Count(); // Kolik je v kolekci?
```

#### **Změny v DataGridu se neuloží**

**Příčina:**
- Nezavolali jste `SaveChanges()`
- Entity nejsou tracked (použili jste `AsNoTracking()`)

**Řešení:**
```csharp
// Ujistěte se, že voláte SaveChanges
_db.SaveChanges();

// Nebo v BtnSave_Click
```

### 📋 Shrnutí kapitoly 10
- Build: Ctrl + Shift + B, Spuštění: F5 (debug) nebo Ctrl + F5
- První spuštění vytvoří databázi a seeduje data
- Testujte všechny CRUD operace (Create, Read, Update, Delete)
- Používejte breakpointy pro debugging
- SSMS umožňuje prohlížet databázi přímo
- Běžné chyby: connection string, missing SaveChanges

### 💡 Cheat Sheet - Testování
| Test | Kroky | Očekávaný výsledek |
|------|-------|-------------------|
| **Zobrazení** | Spustit aplikaci | DataGrid obsahuje 10 studentů |
| **Přidání** | Vyplnit formulář + klik "Přidat" | Nový student v DataGridu |
| **Editace** | Změna v buňce + "Uložit data" | Změny uloženy do DB |
| **Smazání** | Vybrat řádek + "Smazat" | Student zmizí |
| **Perzistence** | Restart aplikace | Data zůstávají |

---

## Kapitola 11: Pokročilé koncepty a best practices

### 11.1 Co jsou migrace?

**Migrace** = Verzování databázového schématu.

**Analogie:**
- Git pro kód → Migrace pro databázi
- Každá změna schématu = commit
- Historie všech změn

### 11.2 EnsureCreated vs Migrace

#### **EnsureCreated() - Pro prototypování**

```csharp
Database.EnsureCreated();
```

**Výhody:**
- ✅ Jednoduché
- ✅ Rychlé pro vývoj

**Nevýhody:**
- ❌ Žádná historie změn
- ❌ Nelze aktualizovat existující DB
- ❌ Ne pro produkci

#### **Migrace - Pro produkci**

```bash
Add-Migration InitialCreate
Update-Database
```

**Výhody:**
- ✅ Historie všech změn
- ✅ Verzování
- ✅ Možnost rollbacku
- ✅ Team collaboration
- ✅ Automatické upgrady

**Nevýhody:**
- ❌ Komplexnější
- ❌ Vyžaduje učení

### 11.3 Přechod z EnsureCreated na migrace

**⚠️ DŮLEŽITÉ:** EnsureCreated a migrace se **vylučují**!

**Krok 1: Odstraňte EnsureCreated()**

```csharp
// MainWindow.xaml.cs
public MainWindow()
{
    InitializeComponent();
    
    // _db.EnsureCreatedAndSeed();  // <-- ZAKOMENTOVAT/SMAZAT
    _db.Database.Migrate();         // <-- PŘIDAT (aplikuje migrace)
    _db.SeedIfEmpty();              // Seed pořád voláme
    
    // ...
}
```

**Krok 2: Smažte existující databázi**

**V SSMS:**
```sql
USE master;
DROP DATABASE StudentDbDemo1;
```

**Nebo pomocí příkazů:**
```bash
sqllocaldb stop MSSQLLocalDB
sqllocaldb delete MSSQLLocalDB
sqllocaldb create MSSQLLocalDB
sqllocaldb start MSSQLLocalDB
```

### 11.4 Vytvoření první migrace

#### **Otevřete Package Manager Console**

- Menu: **Tools → NuGet Package Manager → Package Manager Console**

#### **Vytvořte migraci**

```powershell
Add-Migration InitialCreate
```

**Co se stane:**
1. EF Core analyzuje entity (`Student`)
2. Porovná s existující DB (zatím žádná)
3. Vygeneruje migraci s potřebnými změnami

**Výstup:**
- Složka `Migrations/`
- Soubory:
  - `20260215143000_InitialCreate.cs` - Migrace (Up/Down)
  - `StudentContextModelSnapshot.cs` - Snapshot modelu

#### **Struktura migračního souboru**

```csharp
public partial class InitialCreate : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Students",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:Identity", "1, 1"),
                FirstName = table.Column<string>(maxLength: 30, nullable: false),
                LastName = table.Column<string>(maxLength: 30, nullable: false),
                Year = table.Column<int>(nullable: false),
                Email = table.Column<string>(maxLength: 50, nullable: true),
                CreatedAt = table.Column<DateTime>(nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Students", x => x.Id);
            });
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(
            name: "Students");
    }
}
```

**Up() metoda:**
- Aplikuje změny (vytvoří tabulku)

**Down() metoda:**
- Vrátí změny zpět (smaže tabulku)

### 11.5 Aplikace migrace

```powershell
Update-Database
```

**Co se stane:**
1. EF Core se připojí k databázi
2. Zkontroluje tabulku `__EFMigrationsHistory`
3. Zjistí, které migrace ještě nebyly aplikovány
4. Provede `Up()` metody nových migrací

**Výsledek:**
- Databáze `StudentDbDemo1` existuje
- Tabulka `Students` je vytvořena
- Tabulka `__EFMigrationsHistory` obsahuje záznam o migraci

### 11.6 Přidání nové property - Příklad migrace

#### **Scénář: Přidat telefonní číslo**

**Krok 1: Upravte entitu Student**

```csharp
public class Student
{
    // ... existující properties
    
    [Phone]
    [StringLength(20)]
    public string PhoneNumber { get; set; } = string.Empty;  // <-- NOVÉ
}
```

**Krok 2: Vytvořte migraci**

```powershell
Add-Migration AddPhoneNumberToStudent
```

**Krok 3: Prohlédněte si vygenerovanou migraci**

```csharp
public partial class AddPhoneNumberToStudent : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>(
            name: "PhoneNumber",
            table: "Students",
            maxLength: 20,
            nullable: false,
            defaultValue: "");
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(
            name: "PhoneNumber",
            table: "Students");
    }
}
```

**Krok 4: Aplikujte migraci**

```powershell
Update-Database
```

**Výsledek:**
- Tabulka `Students` má nový sloupec `PhoneNumber`
- Existující data mají `PhoneNumber = ''` (defaultValue)

### 11.7 Rollback migrace

#### **Vrátit se o 1 migraci zpět**

```powershell
Update-Database -Migration AddPhoneNumberToStudent
```

- Vrátí se na stav před `AddPhoneNumberToStudent`

#### **Vrátit se na úplný začátek**

```powershell
Update-Database -Migration 0
```

- Smaže všechny tabulky (zavolá všechny `Down()` metody)

#### **Odstranit poslední migraci**

```powershell
Remove-Migration
```

- **⚠️ POZOR:** Funguje pouze pokud migrace ještě nebyla aplikována!

### 11.8 Migrační příkazy - Reference

| Příkaz | Popis |
|--------|-------|
| `Add-Migration <Name>` | Vytvoří novou migraci |
| `Update-Database` | Aplikuje všechny čekající migrace |
| `Update-Database -Migration <Name>` | Aplikuje/vrátí na konkrétní migraci |
| `Remove-Migration` | Odstraní poslední migraci (pokud nebyla aplikována) |
| `Script-Migration` | Vygeneruje SQL skript pro migrace |
| `Drop-Database` | Smaže databázi |
| `Get-Migration` | Zobrazí seznam migrací |

### 11.9 Pokročilé: Fluent API

**Alternativa k Data Annotations:**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>(entity =>
    {
        // Primární klíč
        entity.HasKey(e => e.Id);
        
        // Identity
        entity.Property(e => e.Id)
            .ValueGeneratedOnAdd();
        
        // Required + MaxLength
        entity.Property(e => e.FirstName)
            .IsRequired()
            .HasMaxLength(30);
        
        // Index
        entity.HasIndex(e => e.Email)
            .IsUnique();
        
        // Default value
        entity.Property(e => e.CreatedAt)
            .HasDefaultValueSql("GETUTCDATE()");
        
        // Check constraint
        entity.HasCheckConstraint("CK_Student_Year", "Year >= 1 AND Year <= 6");
    });
}
```

**Výhody Fluent API:**
- ✅ Více možností než Data Annotations
- ✅ Oddělení persistence logiky od entity
- ✅ Lepší pro komplexní scénáře

### 11.10 Best Practices - Migrace

#### ✅ DOPORUČENO

1. **Pojmenování migrací**
   ```powershell
   Add-Migration AddEmailToStudent       # ✅ Popisné
   Add-Migration CreateCoursesTable      # ✅ Jasné
   ```

2. **Časté commitování**
   - Po každé migraci commitněte do Gitu
   - Tým má přehled o změnách

3. **Kontrola vygenerovaného kódu**
   - Vždy zkontrolujte `Up()` a `Down()` metody
   - EF Core někdy generuje suboptimální SQL

4. **Seed data v migraci**
   ```csharp
   protected override void Up(MigrationBuilder migrationBuilder)
   {
       migrationBuilder.InsertData(
           table: "Students",
           columns: new[] { "FirstName", "LastName", "Year" },
           values: new object[] { "Jan", "Novák", 1 });
   }
   ```

#### ❌ NEDOPORUČENO

1. **Manuální editace migrace po aplikaci**
   - Pokud již byla aplikována, vytvořte novou migraci

2. **Mazání migrací z historie**
   - Narušíte historii

3. **Kombinování EnsureCreated a migrací**
   - Vyberte si jeden přístup

### 📋 Shrnutí kapitoly 11
- Migrace = verzování databázového schématu
- `Add-Migration` vytvoří migraci
- `Update-Database` aplikuje migrace
- `Up()` aplikuje změny, `Down()` je vrací
- EnsureCreated a migrace se vylučují
- Pro produkci používejte migrace
- Fluent API nabízí více možností než Data Annotations

### 💡 Cheat Sheet - Migrace
| Úkol | Příkaz |
|------|--------|
| Vytvořit migraci | `Add-Migration <Name>` |
| Aplikovat migrace | `Update-Database` |
| Vrátit na předchozí | `Update-Database -Migration <Name>` |
| Smazat poslední | `Remove-Migration` |
| Vygenerovat SQL | `Script-Migration` |
| Smazat DB | `Drop-Database` |
| Seznam migrací | `Get-Migration` |

---

## Přílohy

### A. Architektonické vzory - MVVM

**MVVM (Model-View-ViewModel)** je architektonický vzor, který budete používat v pokročilejších cvičeních.

#### **Struktura MVVM**

```
┌─────────┐         ┌──────────────┐         ┌───────┐
│  View   │ ◄──────►│  ViewModel   │ ◄──────►│ Model │
│ (XAML)  │ Binding │   (Logic)    │   Uses  │ (Data)│
└─────────┘         └──────────────┘         └───────┘
```

#### **Náš projekt vs MVVM**

**Současný projekt:**
- View: `MainWindow.xaml`
- ~~ViewModel: Chybí~~
- Model: `Student`, `StudentContext`
- **Code-behind:** `MainWindow.xaml.cs` (obsahuje logiku - není ideální)

**MVVM architektura:**
```
WPF_Aplikace_s_db/
├── Models/
│   ├── Student.cs
│   └── StudentContext.cs
├── ViewModels/
│   ├── MainViewModel.cs         # Logika místo code-behind
│   └── StudentViewModel.cs
├── Views/
│   └── MainWindow.xaml
└── Services/
    └── StudentService.cs        # Business logika
```

**Výhody MVVM:**
- ✅ Separace logiky od UI
- ✅ Snadnější testování
- ✅ Reusability
- ✅ Lepší maintainability

**Pro další cvičení:**
- Přejdete na MVVM pattern
- Použijete MVVM frameworks (CommunityToolkit.Mvvm, Prism, atd.)

### B. Connection String - Všechny možnosti

```csharp
// LocalDB (výchozí pro vývoj)
"Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=StudentDb;Integrated Security=True;"

// SQL Server Express (lokální)
"Data Source=.\\SQLEXPRESS;Initial Catalog=StudentDb;Integrated Security=True;"

// SQL Server (Windows Auth)
"Data Source=localhost;Initial Catalog=StudentDb;Integrated Security=True;"

// SQL Server (SQL Auth)
"Data Source=localhost;Initial Catalog=StudentDb;User Id=sa;Password=YourPassword;"

// Azure SQL Database
"Data Source=yourserver.database.windows.net;Initial Catalog=StudentDb;User Id=yourusername;Password=yourpassword;Encrypt=True;"

// Connection String v appsettings.json (doporučeno pro produkci)
// appsettings.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=..."
  }
}

// Načtení v DbContext
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    var config = new ConfigurationBuilder()
        .AddJsonFile("appsettings.json")
        .Build();
    
    optionsBuilder.UseSqlServer(config.GetConnectionString("DefaultConnection"));
}
```

### C. Užitečné NuGet balíčky

| Balíček | Účel |
|---------|------|
| **Microsoft.EntityFrameworkCore.SqlServer** | SQL Server provider |
| **Microsoft.EntityFrameworkCore.Tools** | Migrační nástroje |
| **Microsoft.EntityFrameworkCore.Proxies** | Lazy loading |
| **PropertyChanged.Fody** | INotifyPropertyChanged |
| **CommunityToolkit.Mvvm** | MVVM helpers |
| **Serilog** | Logging |
| **FluentValidation** | Validace |
| **AutoMapper** | Object mapping |

### D. Klávesové zkratky Visual Studio

| Zkratka | Akce |
|---------|------|
| **Ctrl + Shift + B** | Build solution |
| **F5** | Start debugging |
| **Ctrl + F5** | Start without debugging |
| **F9** | Toggle breakpoint |
| **F10** | Step over |
| **F11** | Step into |
| **Ctrl + .** | Quick Actions |
| **Ctrl + K, Ctrl + D** | Format document |
| **Ctrl + K, Ctrl + C** | Comment selection |
| **Ctrl + K, Ctrl + U** | Uncomment selection |
| **Ctrl + ,** | Navigate to (Go to All) |
| **F12** | Go to definition |
| **Alt + F12** | Peek definition |

### E. Další zdroje

**Oficiální dokumentace:**
- WPF: https://docs.microsoft.com/en-us/dotnet/desktop/wpf/
- Entity Framework Core: https://docs.microsoft.com/en-us/ef/core/
- XAML: https://docs.microsoft.com/en-us/dotnet/desktop/wpf/xaml/

**Video tutoriály:**
- IAmTimCorey (YouTube) - WPF
- Kudvenkat - Entity Framework Core
- Microsoft Learn

**Knihy:**
- Pro WPF 2021 in C# - Matthew MacDonald
- Entity Framework Core in Action - Jon P Smith

---

## Závěr

Gratulujeme! 🎉 Dokončili jste kompletní tutoriál WPF aplikace s Entity Framework Core.

**Co jste se naučili:**
1. ✅ Vytvořit WPF projekt v Visual Studio
2. ✅ Instalovat NuGet balíčky
3. ✅ Definovat databázové entity s validací
4. ✅ Vytvořit DbContext a připojení k LocalDB
5. ✅ Seedovat testovací data
6. ✅ Vytvořit XAML UI s DataGrid
7. ✅ Implementovat data binding
8. ✅ CRUD operace (Create, Read, Update, Delete)
9. ✅ Debugovat aplikaci
10. ✅ Používat databázové migrace

**Důležité koncepty:**
- 🔹 LocalDB je lokální k počítači (nepřenáší se automaticky)
- 🔹 Migrace verzují databázové schéma
- 🔹 Data binding automatizuje synchronizaci UI a dat
- 🔹 ObservableCollection notifikuje UI o změnách
- 🔹 MVVM architektura (použijeme v dalších cvičeních)

**Další kroky:**
1. Experimentujte s kódem
2. Přidejte další entity (např. Course, Enrollment)
3. Implementujte vyhledávání a filtrování
4. Přejděte na MVVM architekturu
5. Přidejte UI validaci

**Pro dotazy a podporu:**
- GitHub Issues
- Stack Overflow
- Microsoft Q&A

---

**Vytvořeno:** 2026-02-15  
**Verze:** 1.0  
**Autor:** Didaktický materiál pro studenty KPV-DBC  
**Framework:** .NET 10, WPF, Entity Framework Core

---

