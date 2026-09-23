```mermaid

classDiagram
class Heros {
  +String nom
  +String pseudonyme
}

```


```mermaid

flowchart TD
    A[Christmas] -->|Get money| B(Go shopping)
    B --> C{Let me think}
    C -->|One| D[Laptop]
    C -->|Two| E[iPhone]
    C -->|Three| F[fa:fa-car Car]

```

```mermaid

usecase-beta
actor User("User")
actor Admin("Administrator")
Login("Log in")
ViewProfile("View profile")
ManageUsers("Manage users")
ViewReports("View reports")
User --> Login
User --> ViewProfile
Admin --> ManageUsers
Admin --> ViewReports

```

```mermaid

usecase-beta
actor Customer("Customer")
actor SupportAgent("Support agent")
systemBoundary "E-commerce System"
  BrowseProducts("Browse products")
  PlaceOrder("Place order")
  TrackOrder("Track order")
end
systemBoundary "Admin Panel"
  ProcessOrders("Process orders")
  HandleReturns("Handle returns")
end
Admin_Panel@{ type: package }
Customer --> BrowseProducts
Customer --> PlaceOrder
Customer --> TrackOrder
SupportAgent --> ProcessOrders
SupportAgent --> HandleReturns

```