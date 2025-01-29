```mermaid
graph TD;
  User-->API_Gateway;
  API_Gateway-->Microservices;
  Microservices-->Database;
