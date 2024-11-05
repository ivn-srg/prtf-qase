<img src="https://github.com/ivn-srg/prtf-qase/blob/main/logo-2.jpeg" alt="Лого" style="width: 50px; height: 50px;"/>

# Mobile app for Qase TMS 

## Описание проекта

Проект представляет собой мобильное приложения для работы с сервисом Qase - системы ведения тестовой документации. Основные функциональности включают в себя: просмотр всех доступных проектов на аккаунте, просмотр всего репозитория тест кейсов, создание проектов и репозиториев в нутри проектов. Пользователи могут авторизовать через свой личный секретный API ключ и пользоваться сервисом на мобильном устройстве.

## Назначение проекта

Этот проект был разработан с целью улучшить взаимодействие с сервисом Qase в виду отсутствия официального мобильного приложения. Он решает проблему отстутствия МП сервиса и удовлетворяет потребности пользователей, связанные с привычным пользованием услугами через мобильные технологии.

## Галерея
<img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.39.58.png" alt="main screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.11.png" alt="orders screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.22.png" alt="auto list screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.30.png" alt="make reservation screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.39.png" alt="profile screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.44.png" alt="profile screen" width="250">

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
  protocol NetworkManager: AnyObject {
      func performRequest<T: Decodable>(
          with data: any Encodable,
          from urlString: String,
          method: HTTPMethod,
          modelType: T.Type
      ) async throws -> T
        
      func makeHTTPRequest<T: Decodable>(
          for request: URLRequest,
          codableModelType: T.Type
      ) async throws -> T
  }

  final class APIManager: NetworkManager {
      static let shared = APIManager()
      
      let DOMEN = "https://api.qase.io/v1"
      
      func performRequest<T: Decodable>(
          with data: any Encodable = Optional<Data>.none,
          from urlString: String,
          method: HTTPMethod,
          modelType: T.Type
      ) async throws -> T {
          guard let url = URL(string: urlString) else { throw APIError.invalidURL }
          
          var request = URLRequest(url: url)
          request.httpMethod = method.rawValue
          request.addValue(TOKEN, forHTTPHeaderField: "Token")
          
          if !(data is Optional<Data>) {
              let jsonData: Data
              do {
                  jsonData = try JSONEncoder().encode(data)
              } catch let error as EncodingError {
                  throw APIError.serializationError(error)
              }
              request.httpBody = jsonData
              request.addValue("application/json", forHTTPHeaderField: "content-type")
          }
          
          if method == .delete || method == .get {
              request.addValue("application/json", forHTTPHeaderField: "accept")
          }
          
          return try await makeHTTPRequest(for: request, codableModelType: modelType)
      }
      
      internal func makeHTTPRequest<T: Decodable>(
          for request: URLRequest,
          codableModelType: T.Type
      ) async throws -> T {
          do {
              let (data, _) = try await URLSession.shared.data(for: request)
              do {
                  let result = try JSONDecoder().decode(codableModelType, from: data)
                  return result
              } catch {
                  let errorModel = try JSONDecoder().decode(ResponseWithErrorModel.self, from: data)
                  let errorMessage = StringError(errorModel.errorMessage)
                  throw APIError.parsingError(errorMessage)
              }
          } catch let error as DecodingError {
              throw APIError.parsingError(error)
          } catch let error as URLError {
              switch error.code {
              case .notConnectedToInternet:
                  throw APIError.noInternetConnection
              case .timedOut:
                  throw APIError.timeout
              default:
                  throw APIError.otherNetworkError(error)
              }
          } catch {
              throw APIError.otherNetworkError(error)
          }
      }
      
      func formUrlString(
          APIMethod: APIEndpoint,
          codeOfProject: String?,
          limit: Int?,
          offset: Int?,
          parentSuite: ParentSuite?,
          caseId: Int?
      ) -> String? {
          
          switch APIMethod {
          case .project:
              if let limit = limit, let offset = offset {
                  return "\(DOMEN)/project?limit=\(limit)&offset=\(offset)"
              } else if let codeOfProject = codeOfProject {
                  return "\(DOMEN)/project/\(codeOfProject)"
              } else {
                  return "\(DOMEN)/project"
              }
          case .suites:
              guard let codeOfProject = codeOfProject else { return nil }
              guard let limit = limit, let offset = offset else {
                  return "\(DOMEN)/suite/\(codeOfProject)"
              }
              guard let parentSuite = parentSuite else {
                  return "\(DOMEN)/suite/\(codeOfProject)?limit=\(limit)&offset=\(offset)"
              }
              guard
                  let searchSuiteString = parentSuite.title.addingPercentEncoding(withAllowedCharacters: .urlHostAllowed)
              else { return nil }
              
              return "\(DOMEN)/suite/\(codeOfProject)?search=\"\(searchSuiteString)\"&limit=\(limit)&offset=\(offset)"
          case .cases:
              guard let codeOfProject = codeOfProject else { return nil }
              guard let limit = limit, let offset = offset else {
                  return "\(DOMEN)/case/\(codeOfProject)"
              }
              guard let parentSuite = parentSuite else {
                  return "\(DOMEN)/case/\(codeOfProject)?limit=\(limit)&offset=\(offset)"
              }
              
              return "\(DOMEN)/case/\(codeOfProject)?suite_id=\(parentSuite.id)&limit=\(limit)&offset=\(offset)"
          case .openedCase:
              guard let codeOfProject = codeOfProject else { return nil }
              guard let caseId = caseId else { return nil }
              
              return "\(DOMEN)/case/\(codeOfProject)/\(caseId)"
          }
      }
  }

  enum APIError: Error {
      case invalidURL, parsingError(Error), serializationError(Error), noInternetConnection, timeout, otherNetworkError(Error)
  }

  enum HTTPMethod: String {
      case get = "GET"
      case post = "POST"
      case patch = "PATCH"
      case delete = "DELETE"
  }

  enum APIEndpoint: String, CaseIterable {
      case project = "project"
      case suites = "suite"
      case cases = "case"
      case openedCase = ""
      
      func returnAllEnumCases() -> [String] {
          var listOfCases = [String]()
          
          for caseValue in APIEndpoint.allCases {
              listOfCases.append(caseValue.rawValue)
          }
          
          return listOfCases
      }
  }

  struct StringError: Error {
      let message: String
      
      init(_ message: String) {
          self.message = message
      }
  }

  extension StringError: LocalizedError {
      var errorDescription: String? {
          return message
      }
  }
  ```
</details>

<details>
  <summary>Экран со списком тест кейсов</summary>

  ```swift
  import UIKit

  final class SuitesAndCasesTableViewController: UIViewController {

      var viewModel: SuitesAndCasesViewModel
      
      // MARK: - UI
      
      private lazy var tableVw: UITableView = {
          let tv = UITableView()
          tv.translatesAutoresizingMaskIntoConstraints = false
          tv.rowHeight = UITableView.automaticDimension
          tv.estimatedRowHeight = 44
          tv.register(SuitesAndCasesTableViewCell.self, forCellReuseIdentifier: SuitesAndCasesTableViewCell.cellId)
          return tv
      }()
      
      private let emptyDataLabel: UILabel = {
          let label = UILabel()
          label.translatesAutoresizingMaskIntoConstraints = false
          label.text = "There's nothing here yet 🙁"
          label.textAlignment = .center
          label.textColor = .gray
          label.numberOfLines = 0
          label.isHidden = true
          return label
      }()
      
      // MARK: - Lifecycles
      
      init(parentSuite: ParentSuite? = nil) {
          self.viewModel = parentSuite != nil ? SuitesAndCasesViewModel(parentSuite: parentSuite) : SuitesAndCasesViewModel()
          super.init(nibName: nil, bundle: nil)
      }
      
      required init?(coder: NSCoder) {
          fatalError("init(coder:) has not been implemented")
      }
      
      override func viewDidLoad() {
          super.viewDidLoad()
          
          viewModel.delegate = self
          setupTableView()
          navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addNewEntity))
      }
      
      override func viewWillAppear(_ animated: Bool) {
          super.viewWillAppear(true)
          viewModel.requestEntitiesData()
      }
      
      // MARK: - Setuping UI for tableView
      func setupTableView() {
          view.backgroundColor = AppTheme.bgPrimaryColor
          title = viewModel.parentSuite == nil ? PROJECT_NAME
          : self.viewModel.suitesAndCaseData.filter( {$0.isSuites && $0.id == self.viewModel.parentSuite?.id} ).first?.title
          
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
          let vc = CreateSuiteOrCaseViewController(viewModel: .init())
          vc.modalPresentationStyle = .fullScreen
          present(vc, animated: true)
      }
  }

  // MARK: - UpdateTableViewProtocol
  extension SuitesAndCasesTableViewController: UpdateTableViewProtocol {
      func updateTableView() {
          DispatchQueue.main.async {
              self.tableVw.reloadData()
          }
      }
  }

  // MARK: - NextViewControllerPusher
  extension SuitesAndCasesTableViewController: NextViewControllerPusher {
      func pushToNextVC(to item: Int?) {
          guard let item = item else { return }
          let vc: UIViewController
          let parentSuite = ParentSuite(id: viewModel.suitesAndCaseData[item].id, title: viewModel.suitesAndCaseData[item].title)
          let caseItem = viewModel.suitesAndCaseData[item]
          
          if viewModel.suitesAndCaseData[item].isSuites {
              vc = SuitesAndCasesTableViewController(parentSuite: parentSuite)
          } else {
              vc = DetailTabBarController(caseId: caseItem.id)
          }
          self.navigationController?.pushViewController(vc, animated: true)
      }
  }

  // MARK: - Table view data source
  extension SuitesAndCasesTableViewController: UITableViewDataSource {
      
      func numberOfSections(in tableView: UITableView) -> Int {
          updateEmptyDataLabelVisibility()
          return viewModel.suitesAndCaseData.count > 0 ? 1 : 0
      }
      
      func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
          title
      }
      
      func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
          viewModel.countOfRows()
      }
      
      func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
          guard let cell = tableView.dequeueReusableCell(withIdentifier: SuitesAndCasesTableViewCell.cellId, for: indexPath) as? SuitesAndCasesTableViewCell else { return UITableViewCell() }
          
          let dataForCell = viewModel.suitesAndCaseData[indexPath.row]
          cell.configure(with: dataForCell)
          
          return cell
          
      }
      
      func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
          pushToNextVC(to: indexPath.row)
      }
  }

  extension SuitesAndCasesTableViewController: UITableViewDelegate {
      func tableView(_ tableView: UITableView, estimatedHeightForRowAt indexPath: IndexPath) -> CGFloat {
          return UITableView.automaticDimension
      }
  }
  ```
</details>

# Основные достижения и результаты

- Успешная реализация приложения для ведения тестовой документации с локальным кешированием.
- Реализация интуитивно понятного пользовательского интерфейса, который позволяет приятно отображать весь репозиторий.
- Оптимизация производительности UI части, гарантирующая понятную и отзывчивую работу приложения.
- Создание детальных страниц тест кейсов, предоставляющих всю необходимую информацию в удобном формате.
- Создание экранов создания сущностей: проекта, тестового набора или тест кейса.

## Ссылки

- **Код проекта**: Код находится <a href="https://github.com/ivn-srg/iOSPetProjectWithQaseAPI">здесь</a>.
