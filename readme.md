**a. Реализованный функционал и его расположение:**

Следующие пункты требуемого функционала были реализованы в рамках проекта. Их реализация распределена по классам и модулям согласно принципам Clean Architecture:

1.  **Добавить животное:**
    *   **Web:** `Web/Controllers/AnimalsController.cs` (метод `CreateAnimal`), `Web/Models/CreateAnimalRequest.cs` (модель запроса)
    *   **Application:** `Application/Services/AnimalManagementService.cs` (метод `CreateAnimalAsync`)
    *   **Domain:** `Domain/Entities/Animal.cs` (фабричный метод `Create`), `Domain/Interfaces/IAnimalRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryAnimalRepository.cs` (метод `AddAsync`)

2.  **Удалить животное:**
    *   **Web:** `Web/Controllers/AnimalsController.cs` (метод `DeleteAnimal`)
    *   **Application:** `Application/Services/AnimalManagementService.cs` (метод `DeleteAnimalAsync`)
    *   **Domain:** `Domain/Interfaces/IAnimalRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryAnimalRepository.cs` (метод `DeleteAsync`)

3.  **Добавить вольер:**
    *   **Web:** `Web/Controllers/EnclosuresController.cs` (метод `CreateEnclosure`), `Web/Models/CreateEnclosureRequest.cs`
    *   **Application:** `Application/Services/EnclosureManagementService.cs` (метод `CreateEnclosureAsync`)
    *   **Domain:** `Domain/Entities/Enclosure.cs` (фабричный метод `Create`), `Domain/Interfaces/IEnclosureRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryEnclosureRepository.cs` (метод `AddAsync`)

4.  **Удалить вольер:**
    *   **Web:** `Web/Controllers/EnclosuresController.cs` (метод `DeleteEnclosure`)
    *   **Application:** `Application/Services/EnclosureManagementService.cs` (метод `DeleteEnclosureAsync`) - включает проверку на наличие животных в вольере.
    *   **Domain:** `Domain/Interfaces/IEnclosureRepository.cs`, `Domain/Interfaces/IAnimalRepository.cs` (для проверки)
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryEnclosureRepository.cs` (метод `DeleteAsync`), `Infrastructure/Persistence/InMemoryAnimalRepository.cs`

5.  **Переместить животное между вольерами:**
    *   **Web:** `Web/Controllers/AnimalsController.cs` (метод `MoveAnimal`), `Web/Models/MoveAnimalRequest.cs`
    *   **Application:** `Application/Services/AnimalTransferService.cs` (метод `MoveAnimalAsync`) - координирует процесс, проверяет совместимость и вместимость.
    *   **Domain:** `Domain/Entities/Animal.cs` (метод `AssignToEnclosure`), `Domain/Entities/Enclosure.cs` (методы `IsCompatible`, `CanAccommodate`), `Domain/Interfaces/IAnimalRepository.cs`, `Domain/Interfaces/IEnclosureRepository.cs`, `Domain/Events/AnimalMovedEvent.cs` (событие генерируется)
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryAnimalRepository.cs`, `Infrastructure/Persistence/InMemoryEnclosureRepository.cs`, `Infrastructure/Persistence/InMemoryDataStore.cs` (обновление счетчиков).

6.  **Просмотреть расписание кормления (общее и для животного):**
    *   **Web:** `Web/Controllers/FeedingController.cs` (методы `GetAllSchedules`, `GetAnimalSchedule`)
    *   **Application:** `Application/Services/FeedingOrganizationService.cs` (методы `GetAllSchedulesAsync`, `GetAnimalScheduleAsync`)
    *   **Domain:** `Domain/Entities/FeedingSchedule.cs`, `Domain/Interfaces/IFeedingScheduleRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryFeedingScheduleRepository.cs` (методы `GetAllAsync`, `GetSchedulesForAnimalAsync`)

7.  **Добавить новое кормление в расписание:**
    *   **Web:** `Web/Controllers/FeedingController.cs` (метод `AddFeedingSchedule`), `Web/Models/CreateFeedingRequest.cs`
    *   **Application:** `Application/Services/FeedingOrganizationService.cs` (метод `AddFeedingScheduleAsync`)
    *   **Domain:** `Domain/Entities/FeedingSchedule.cs` (фабричный метод `Create`), `Domain/Interfaces/IFeedingScheduleRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryFeedingScheduleRepository.cs` (метод `AddAsync`)

8.  **Просмотреть статистику зоопарка:**
    *   **Web:** `Web/Controllers/StatisticsController.cs` (метод `GetZooStatistics`)
    *   **Application:** `Application/Services/ZooStatisticsService.cs` (метод `GetStatisticsAsync`), `Application/Results/ZooStatisticsResult.cs` (структура данных для статистики)
    *   **Domain:** `Domain/Interfaces/IAnimalRepository.cs`, `Domain/Interfaces/IEnclosureRepository.cs`
    *   **Infrastructure:** `Infrastructure/Persistence/InMemoryAnimalRepository.cs`, `Infrastructure/Persistence/InMemoryEnclosureRepository.cs`

9.  **Дополнительный реализованный функционал (вытекающий из модели):**
    *   **Просмотр информации о конкретном животном/вольере:** `AnimalsController.GetAnimal`, `EnclosuresController.GetEnclosure` -> соответствующие методы сервисов и репозиториев.
    *   **Лечение животного:** `AnimalsController.TreatAnimal` -> `AnimalManagementService.TreatAnimalAsync` -> `Domain/Entities/Animal.cs` (метод `Treat`).
    *   **Отметить животное как больное:** `AnimalsController.MarkAnimalSick` -> `AnimalManagementService.MarkAnimalSickAsync` -> `Domain/Entities/Animal.cs` (метод `MarkAsSick`).
    *   **Отметить кормление выполненным:** `FeedingController.MarkAsCompleted` -> `FeedingOrganizationService.MarkFeedingAsCompletedAsync` -> `Domain/Entities/FeedingSchedule.cs` (метод `MarkAsCompleted`).
    *   **Выполнить кормление (симуляция):** `FeedingController.PerformFeeding` -> `FeedingOrganizationService.PerformFeedingAsync` -> `Domain/Entities/Animal.cs` (метод `Feed`), `Domain/Entities/FeedingSchedule.cs` (метод `MarkAsCompleted`).
    *   **Уборка вольера (симуляция):** `EnclosuresController.CleanEnclosure` -> `EnclosureManagementService.CleanEnclosureAsync` -> `Domain/Entities/Enclosure.cs` (метод `Clean`).

---

**b. Примененные концепции DDD и принципы Clean Architecture:**

При разработке были применены следующие концепции и принципы:

**Концепции Domain-Driven Design (DDD):**

*   **Ubiquitous Language (Единый язык):** Использованы термины предметной области (`Animal`, `Enclosure`, `FeedingSchedule`, `Species`, `Treat`, `Feed`, `Move`, `Clean` и т.д.) в именовании классов, методов и свойств во всех слоях, особенно в `Domain` и `Application`.
*   **Entities (Сущности):** Определены ключевые сущности с идентификаторами и жизненным циклом: `Animal`, `Enclosure`, `FeedingSchedule` в модуле `ZooManagement.Domain/Entities`.
*   **Value Objects (Объекты-значения):** Использованы простые объекты-значения в виде перечислений (`enum`) для описательных характеристик без собственной идентичности: `AnimalStatus`, `AnimalSex`, `EnclosureType` в `ZooManagement.Domain/Entities` (или `Domain/Enums`).
*   **Rich Domain Model (Богатая модель):** Логика и поведение, связанные с сущностями, инкапсулированы внутри самих классов сущностей, а не размазаны по сервисам. Примеры: `Animal.Feed()`, `Animal.Treat()`, `Animal.MarkAsSick()`, `Animal.AssignToEnclosure()`, `Enclosure.IsCompatible()`, `Enclosure.CanAccommodate()`, `Enclosure.Clean()`, `FeedingSchedule.MarkAsCompleted()`. Все в `ZooManagement.Domain/Entities`.
*   **Repositories (Репозитории):** Определены интерфейсы репозиториев в доменном слое (`IAnimalRepository`, `IEnclosureRepository`, `IFeedingScheduleRepository` в `ZooManagement.Domain/Interfaces`) для абстрагирования способа хранения данных. Реализации находятся в инфраструктурном слое.
*   **Domain Events (Доменные события):** Определены события, отражающие важные изменения в домене (`AnimalMovedEvent`, `FeedingTimeEvent` в `ZooManagement.Domain/Events`). Событие `AnimalMovedEvent` генерируется методом `Animal.AssignToEnclosure()`.
*   **Application Services (Сервисы приложения):** Созданы сервисы (`AnimalTransferService`, `FeedingOrganizationService`, `ZooStatisticsService`, `AnimalManagementService`, `EnclosureManagementService` в `ZooManagement.Application/Services`) для оркестрации вариантов использования (use cases). Они координируют работу с репозиториями и доменными объектами, но не содержат основной бизнес-логики.
*   **Factories (Фабрики):** Использованы статические фабричные методы `Create()` внутри сущностей (`Animal`, `Enclosure`, `FeedingSchedule`) для инкапсуляции логики создания объектов и обеспечения их валидности при создании (`ZooManagement.Domain/Entities`).

**Принципы Clean Architecture:**

*   **Layers (Слои):** Проект четко разделен на слои с разной ответственностью:
    *   `ZooManagement.Domain`: Ядро с бизнес-логикой и моделями.
    *   `ZooManagement.Application`: Логика приложения, координация use cases.
    *   `ZooManagement.Infrastructure`: Взаимодействие с внешним миром (в данном случае - in-memory хранилище).
    *   `ZooManagement.Web`: Пользовательский интерфейс (Web API).
*   **Dependency Rule (Правило зависимостей):** Зависимости направлены строго внутрь: `Web` зависит от `Application`; `Infrastructure` зависит от `Application` и `Domain`; `Application` зависит только от `Domain`. Слой `Domain` не имеет внешних зависимостей. Это видно по ссылкам между проектами и `using` директивам в коде.
*   **Domain Layer (Доменный слой):** Является ядром приложения (`ZooManagement.Domain`), не зависит от деталей реализации (UI, БД). Содержит сущности, VO, события, интерфейсы репозиториев.
*   **Application Layer (Слой приложения):** (`ZooManagement.Application`) Содержит логику конкретных вариантов использования, не зависит от UI и базы данных напрямую, работает с интерфейсами репозиториев.
*   **Infrastructure Layer (Инфраструктурный слой):** (`ZooManagement.Infrastructure`) Реализует интерфейсы, определенные в `Domain` (репозитории `InMemory...Repository` в `Infrastructure/Persistence`). Содержит конкретные детали (in-memory хранилище `InMemoryDataStore`).
*   **Web Layer (Слой представления):** (`ZooManagement.Web`) Отвечает за взаимодействие с пользователем/клиентом через Web API (`Controllers`). Зависит от `Application` для вызова бизнес-логики. Настройка зависимостей (DI) происходит здесь, в `Program.cs`.
*   **Dependency Inversion Principle (Принцип инверсии зависимостей):** Высокоуровневые модули (`Application`) не зависят от низкоуровневых (`Infrastructure`), оба зависят от абстракций (`Domain` Interfaces). Зависимости внедряются через конструкторы с использованием DI контейнера ASP.NET Core (настройка в `Program.cs`).

