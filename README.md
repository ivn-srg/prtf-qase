<img src="https://github.com/ivn-srg/prtf-qase/blob/main/logo-2.jpeg" alt="Лого" style="width: 50px; height: 50px;"/>

# Mobile app for Qase TMS 

## Описание проекта

Проект представляет собой мобильное приложения для работы с сервисом Qase - системы ведения тестовой документации. Основные функциональности включают в себя: просмотр всех доступных проектов на аккаунте, просмотр всего репозитория тест кейсов, создание проектов и репозиториев в нутри проектов. Пользователи могут авторизовать через свой личный секретный API ключ и пользоваться сервисом на мобильном устройстве.

## Назначение проекта

Этот проект был разработан с целью улучшить взаимодействие с сервисом Qase в виду отсутствия официального мобильного приложения. Он решает проблему отстутствия МП сервиса и удовлетворяет потребности пользователей, связанные с привычным пользованием услугами через мобильные технологии.

## Галерея
<img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.54.55.png" alt="main screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.11.png" alt="projects screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.07.png" alt="creating project screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.44.png" alt="test entities list screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.52.png" alt="creating test entity screen 1" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.05.png" alt="creating test entity screen 2" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.15.png" alt="detail test case main screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.19.png" alt="detail test case screen with properties" width="250">

## Моя роль в проекте

Данный проект является индивидуальным, соответственно, все стадии разработки, начиная от проектирования, создания макетов и заканчивая сетевым менеджером, разрабатывались мною лично.


## Технологии, используемые в проекте

![Realm](https://img.shields.io/badge/Realm-5B8C4A?style=for-the-badge&logo=realm&logoColor=white)
![Swift](https://img.shields.io/badge/Swift-F05138?style=for-the-badge&logo=swift&logoColor=white)
![Apple UIKit](https://img.shields.io/badge/UIKit-007AFF?style=for-the-badge&logo=apple&logoColor=white)
![SnapKit](https://img.shields.io/badge/SnapKit-FF4B30?style=for-the-badge&logo=swift&logoColor=white)
![Async/Await](https://img.shields.io/badge/Async%20Await-4BCB1A?style=for-the-badge&logo=swift&logoColor=white)
![MVVM](https://img.shields.io/badge/MVVM-FF3D00?style=for-the-badge&logo=swift&logoColor=white)

## Архитектура проекта

Применены архитектурные паттерны Clean Architecture, MVVM, ориентированные на разделение проекта по функциональным блокам для улучшения масштабируемости и удобства поддержки и тестирования.

<img src="https://github.com/ivn-srg/prtf-qase/blob/main/Снимок%20экрана%202024-11-05%20в%2018.01.59.png" alt="xcode code structure screen" width="250">


## Принципы и инструменты разработки
- Система контроля версий: Github
- IDE: Xcode

## Код: Предпросмотр

В данном разделе представлены отрывки кода, демонстрирующие ключевые моменты и методы, использованные в проекте. Эти фрагменты кода отражают стиль программирования, архитектурные решения и технологические практики, применённые в ходе работы.

<details>
  <summary>Сетевой менеджер</summary>
  
  ```swift
  struct API {
    static let BASE_URL = "https://api.qase.io/v1"
    
    enum NetError: Error {
        case invalidURL, parsingError(String), serializationError(String),
             noInternetConnection, timeout, otherNetworkError(String), invalidCredantials
    }
    
    enum HTTPMethod: String {
        case get = "GET"
        case post = "POST"
        case patch = "PATCH"
        case delete = "DELETE"
    }
    
    enum Endpoint: String, CaseIterable {
        case project = "project"
        case suites = "suite"
        case cases = "case"
        
        func returnAllEnumCases() -> [String] {
            var listOfCases = [String]()
            
            for caseValue in Self.allCases {
                listOfCases.append(caseValue.rawValue)
            }
            
            return listOfCases
        }
    }
    
    enum QueryParams: String {
        case limit = "limit"
        case offset = "offset"
        case suiteId = "suite_id"
        case search = "search"
    }
  }

  protocol NetworkManager: AnyObject {
      func performRequest<T: Decodable>(
          with data: Encodable?,
          from urlString: URL?,
          method: API.HTTPMethod,
          modelType: T.Type
      ) async throws(API.NetError) -> T
      
      func composeURL(for method: API.Endpoint, urlComponents: [String?]?, queryItems: [API.QueryParams: Int?]?) -> URL?
      
      func auth(by token: String) async throws -> Bool
  }
  
  final class APIManager: NetworkManager {
      static let shared = APIManager()
      
      func auth(by token: String) async throws -> Bool {
          guard
              !token.isEmpty,
              let url = composeURL(for: .project, urlComponents: nil)
          else {
              return false
          }
          
          var request = URLRequest(url: url)
          request.httpMethod = "GET"
          request.addValue(token, forHTTPHeaderField: "Token")
          
          let (_, response) = try await URLSession.shared.data(for: request)
          
          guard let httpResponse = response as? HTTPURLResponse else {
              throw URLError(.badServerResponse)
          }
          
          guard (200...299).contains(httpResponse.statusCode) else {
              return false
          }
          
          return true
      }
      
      func performRequest<T: Decodable>(
          with data: Encodable? = nil,
          from urlString: URL?,
          method: API.HTTPMethod,
          modelType: T.Type
      ) async throws(API.NetError) -> T {
          guard
              let url = urlString,
              let authToken = AuthManager.shared.getAuthToken()
          else { throw .invalidURL }
          
          var request = URLRequest(url: url)
          request.httpMethod = method.rawValue
          request.addValue(authToken, forHTTPHeaderField: "Token")
          
          if let data = data {
              let encoder = JSONEncoder()
              let jsonData: Data
              encoder.keyEncodingStrategy = .convertToSnakeCase
              do {
                  jsonData = try encoder.encode(data)
                  request.httpBody = jsonData
                  request.addValue("application/json", forHTTPHeaderField: "content-type")
              } catch {
                  throw .serializationError(error.localizedDescription)
              }
          }
          
          if method == .delete || method == .get {
              request.addValue("application/json", forHTTPHeaderField: "accept")
          }
          
          return try await makeHTTPRequest(for: request, codableModelType: modelType)
      }
      
      private func makeHTTPRequest<T: Decodable>(
          for request: URLRequest,
          codableModelType: T.Type
      ) async throws(API.NetError) -> T {
          var errorMessage = ""
          
          do {
              let (data, _) = try await URLSession.shared.data(for: request)
              do {
                  let result = try JSONDecoder().decode(codableModelType, from: data)
                  return result
              } catch {
                  let errorModel = try JSONDecoder().decode(ResponseWithErrorModel.self, from: data)
                  
                  errorMessage = errorModel.errorMessage != nil
                  ? errorModel.errorMessage!
                  : errorModel.message != nil ? errorModel.message! : ""
              }
              throw API.NetError.parsingError(errorMessage)
          } catch let error as DecodingError {
              throw .parsingError(error.errorDescription ?? error.localizedDescription)
          } catch {
              guard errorMessage.isEmpty else { throw .parsingError(errorMessage) }
              throw .otherNetworkError(error.localizedDescription)
          }
      }
      
      func composeURL(for method: API.Endpoint, urlComponents: [String?]?, queryItems: [API.QueryParams: Int?]? = nil) -> URL? {
          
          var resultUrl = URL(string: "\(API.BASE_URL)/\(method.rawValue)")
          
          if let urlComponents = urlComponents {
              for component in urlComponents {
                  guard let component = component else { continue }
                  
                  resultUrl = resultUrl?.appendingPathComponent(component)
              }
          }
          
          if let queryItems = queryItems {
              var queryDict = [URLQueryItem]()
              
              for item in queryItems {
                  guard let itemValue = item.value else { continue }
                  
                  queryDict.append(URLQueryItem(name: item.key.rawValue, value: "\(itemValue)"))
              }
              
              resultUrl?.append(queryItems: queryDict)
          }
          
          return resultUrl
      }
  }
  
  final class APIMockManager: NetworkManager {
      static let shared = APIMockManager()
      
      func auth(by token: String) async throws -> Bool { true }
      
      func performRequest<T: Decodable>(
          with data: Encodable? = nil,
          from urlString: URL?,
          method: API.HTTPMethod,
          modelType: T.Type
      ) async throws(API.NetError) -> T {
          guard let url = urlString else { throw .invalidURL }
          
          return try await makeHTTPRequest(for: URLRequest(url: url), codableModelType: modelType)
      }
      
      private func makeHTTPRequest<T: Decodable>(
          for request: URLRequest,
          codableModelType: T.Type
      ) async throws(API.NetError) -> T {
          let errorMessage = ""
          
          do {
              try await Task.sleep(nanoseconds: 2000)
              
              let path = request.url?.pathComponents.last!
              
              let inputCase = API.Endpoint(rawValue: path ?? "")
              var resourceName: String
              
              switch inputCase {
              case .project:
                  resourceName = "project_data"
              case .suites:
                  resourceName = "suites_data"
              case .cases:
                  resourceName = "test_cases_data"
              case nil:
                  resourceName = "project_data"
              }
              
              guard let filePath = Bundle.main.path(forResource: "\(resourceName)", ofType: "json")
              else {
                  print("Файл не найден")
                  throw API.NetError.invalidURL
              }
              
              let data = try Data(contentsOf: URL(fileURLWithPath: filePath))
              
              let result = try JSONDecoder().decode(codableModelType, from: data)
              return result
              
          } catch let error as DecodingError {
              throw .parsingError(error.errorDescription ?? error.localizedDescription)
          } catch {
              guard errorMessage.isEmpty else { throw .parsingError(errorMessage) }
              throw .otherNetworkError(error.localizedDescription)
          }
      }
      
      func composeURL(for method: API.Endpoint, urlComponents: [String?]?, queryItems: [API.QueryParams: Int?]? = nil) -> URL? {
          
          var resultUrl = URL(string: "\(API.BASE_URL)/\(method.rawValue)")
          
          if let urlComponents = urlComponents {
              for component in urlComponents {
                  guard let component = component else { continue }
                  
                  resultUrl = resultUrl?.appendingPathComponent(component)
              }
          }
          
          if let queryItems = queryItems {
              var queryDict = [URLQueryItem]()
              
              for item in queryItems {
                  guard let itemValue = item.value else { continue }
                  
                  queryDict.append(URLQueryItem(name: item.key.rawValue, value: "\(itemValue)"))
              }
              
              resultUrl?.append(queryItems: queryDict)
          }
          
          return resultUrl
      }
  }
  
  
  final class ApiServiceConfiguration {
      public static let shared = ApiServiceConfiguration()
      
      private init() {}
      
      var apiService: NetworkManager {
          if shouldUseMockingService {
              return APIMockManager.shared
          } else {
              return APIManager.shared
          }
      }
      
      private var shouldUseMockingService: Bool = false
      
      func setMockingServiceEnabled() {
          shouldUseMockingService = true
      }
  }
  ```
</details>

<details>
  <summary>Экран со списком тест кейсов</summary>

  ```swift
  final class TestEntitiesTableViewController: UIViewController {
    
    var viewModel: TestEntitiesViewModel
    
    // MARK: - UI
    
    private lazy var tableVw: UITableView = {
        let tv = UITableView()
        tv.translatesAutoresizingMaskIntoConstraints = false
        tv.rowHeight = UITableView.automaticDimension
        tv.estimatedRowHeight = 44
        tv.register(SuitesAndCasesTableViewCell.self, forCellReuseIdentifier: SuitesAndCasesTableViewCell.cellId)
        return tv
    }()
    
    private let emptyDataLabel: UILabel = EmptyDataLabel()
    
    // MARK: - Lifecycles
    
    init(parentSuite: ParentSuite? = nil) {
        viewModel = parentSuite != nil
        ? TestEntitiesViewModel(parentSuite: parentSuite)
        : TestEntitiesViewModel()
        
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        viewModel.delegate = self
        setupTableView()
        configureRefreshControls()
        navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addNewEntity))
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(true)
        executeWithErrorHandling {
            try await self.viewModel.requestEntitiesData(place: .start)
        }
    }
    
    // MARK: - Setuping UI for tableView
    func setupTableView() {
        view.backgroundColor = AppTheme.bgPrimaryColor
        title = viewModel.parentSuite == nil ? PROJECT_NAME
        : self.viewModel.testEntitiesData.filter( {$0.isSuite && $0.id == self.viewModel.parentSuite?.id} ).first?.title
        
        tableVw.delegate = self
        tableVw.dataSource = self
        
        view.addSubview(tableVw)
        tableVw.addSubview(emptyDataLabel)
        
        tableVw.snp.makeConstraints {
            $0.edges.equalTo(view.safeAreaLayoutGuide.snp.edges)
        }
        emptyDataLabel.snp.makeConstraints {
            $0.center.equalToSuperview()
        }
    }
    
    private func updateEmptyDataLabelVisibility() {
        emptyDataLabel.isHidden = viewModel.countOfRows() > 0
    }
    
    // MARK: - @objc funcs
    @objc func addNewEntity() {
        let vc = CreateSuiteOrCaseViewController(viewModel: .init(parentSuiteId: viewModel.parentSuite?.id))
        vc.modalPresentationStyle = .fullScreen
        present(vc, animated: true)
    }
  }
  
  // MARK: - UpdateTableViewProtocol
  extension TestEntitiesTableViewController: UpdateTableViewProtocol {
      func updateTableView() {
          Task { @MainActor in
              tableVw.reloadData()
          }
      }
  }
  
  // MARK: - NextViewControllerPusher
  extension TestEntitiesTableViewController: NextViewControllerPusher {
      func pushToNextVC(to item: Int?) {
          guard let item = item else { return }
          
          let vc: UIViewController
          let testEntityItem = viewModel.testEntitiesData[item]
          let parentSuite = ParentSuite(id: testEntityItem.id, title: testEntityItem.title, codeOfProject: PROJECT_NAME)
          let caseItem = viewModel.testEntitiesData[item]
          
          if testEntityItem.isSuite {
              vc = TestEntitiesTableViewController(parentSuite: parentSuite)
          } else {
              vc = TestCaseViewController(caseUniqueKey: "\(caseItem.id)_\(PROJECT_NAME)")
          }
          navigationController?.pushViewController(vc, animated: true)
      }
  }
  
  // MARK: - Table view data source
  extension TestEntitiesTableViewController: UITableViewDataSource {
      
      func numberOfSections(in tableView: UITableView) -> Int {
          updateEmptyDataLabelVisibility()
          return viewModel.testEntitiesData.count > 0 ? 1 : 0
      }
      
      func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
          title
      }
      
      func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
          viewModel.countOfRows()
      }
      
      func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
          guard let cell = tableView.dequeueReusableCell(
              withIdentifier: SuitesAndCasesTableViewCell.cellId,
              for: indexPath) as? SuitesAndCasesTableViewCell
          else { return UITableViewCell() }
          
          let dataForCell = viewModel.testEntitiesData[indexPath.row]
          cell.configure(with: dataForCell)
          
          return cell
      }
      
      func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
          pushToNextVC(to: indexPath.row)
      }
  }
  
  // MARK: - Table view delegate
  extension TestEntitiesTableViewController: UITableViewDelegate {
      func tableView(_ tableView: UITableView, estimatedHeightForRowAt indexPath: IndexPath) -> CGFloat {
          return UITableView.automaticDimension
      }
      
      func tableView(_ tableView: UITableView, trailingSwipeActionsConfigurationForRowAt indexPath: IndexPath) -> UISwipeActionsConfiguration? {
          let swipeAction = UIContextualAction(style: .destructive, title: "Delete".localized) { [weak self] _, _, completionHandler in
              guard let self = self else { return }
              let entity = viewModel.testEntitiesData[indexPath.row]
              let entityName = entity.isSuite ? "Test suite".localized : "Test case".localized
              let composedMessage = String(format: "confirmMessage".localized, entityName.localized.lowercased(), "")
              
              UIAlertController.showConfirmAlert(
                  on: self,
                  title: "Confirmation".localized,
                  message: composedMessage) { _ in
                      self.executeWithErrorHandling {
                          try await self.viewModel.deleteEntity(at: indexPath.row)
                      }
                      completionHandler(true)
                  } cancelCompetionHandler: { _ in
                      completionHandler(false)
                  }
          }
          swipeAction.image = AppTheme.trashImage
          return UISwipeActionsConfiguration(actions: [swipeAction])
      }
      
      func tableView(_ tableView: UITableView, willDisplay cell: UITableViewCell, forRowAt indexPath: IndexPath) {
          if viewModel.testEntitiesData.count - indexPath.row <= 3 && !viewModel.isLoading {
              executeWithErrorHandling { [weak self] in
                  try await self?.viewModel.requestEntitiesData(place: .continuos)
              }
          }
      }
  }
  
  extension TestEntitiesTableViewController {
      func configureRefreshControls() {
          tableVw.refreshControl = UIRefreshControl()
          tableVw.refreshControl?.addTarget(self,
                                            action: #selector(handleRefreshControl),
                                            for: .valueChanged)
          
          
          let activityIndicatorView = UIActivityIndicatorView(style: .medium)
          tableVw.tableFooterView = activityIndicatorView
      }
      
      @objc func handleRefreshControl() {
          executeWithErrorHandling {
              try await self.viewModel.requestEntitiesData(place: .start)
          }
          
          tableVw.refreshControl?.endRefreshing()
      }
  }
  ```
</details>

# Основные достижения и результаты

- Настройка базовой CI/CD на github actions
- Написание unit/ui тестов
- Успешная реализация приложения для ведения тестовой документации с локальным кешированием.
- Реализация интуитивно понятного пользовательского интерфейса, который позволяет приятно отображать весь репозиторий.
- Оптимизация производительности UI части, гарантирующая понятную и отзывчивую работу приложения.
- Создание детальных страниц тест кейсов, предоставляющих всю необходимую информацию в удобном формате.
- Создание экранов создания сущностей: проекта, тестового набора или тест кейса.

## Ссылки

- **Код проекта**: Код находится <a href="https://github.com/ivn-srg/iOSPetProjectWithQaseAPI">здесь</a>.
