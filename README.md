# quiz-sd
## quiz system design

This architecture design ensures that the real-time quiz feature functions smoothly with accurate and consistent score updates and prompt leaderboard changes

- Here is the C4 diagram

<img width="994" alt="image" src="https://github.com/duminhtam/quiz-sd/assets/1624637/af03ec9c-7166-47db-ac8a-79a7b1d7f81d">

- Here is the Seuquence diagram

<img width="826" alt="image" src="https://github.com/duminhtam/quiz-sd/assets/1624637/5691b125-4af8-4ad5-a2e5-9b9b9b23c04a">

## Components
- Client Applications: Interfaces for users to participate in the quiz, answer questions, and view the leaderboard.
- Kong API Gateway: Acts as a centralized entry point for handling API requests and routing them appropriately.
- Redis Cache Layer: Stores temporary data such as user scores, quiz sessions, and leaderboard information for quick access.
- Transaction Server: Manages quiz logic, real-time scoring, and leaderboard updates.
- Kafka Message Bus: Facilitates real-time messaging and communication between components for score updates and leaderboard changes.
- WebSocket: Enables bidirectional and real-time communication between clients and the server for instant updates.
## Interaction Flow:

###  User Participation: 
- Clients request to join a quiz session through the Kong API Gateway and are assigned a unique quiz ID by the Transaction Server.
- Multiple users can join the same quiz session simultaneously, managed by the Transaction Server.
- Users join a quiz session using a unique quiz ID.
- The API Gateway forwards the request to the Transaction Server, which then checks and adds the user to the quiz session in the Redis Cache.
- Confirmation of the user joining is sent back through the same path to the Client App, which confirms it to the User.

### Real-Time Score Updates:
- When users submit answers, the Transaction Server processes them, updates scores in Redis, and sends real-time score updates via Kafka.
- Clients receive score updates in real-time through WebSocket connections.
- When a user submits an answer, it is sent from the Client App via WebSocket to the Transaction Server.
- The Transaction Server processes the answer and updates the user score in the Redis Cache.
- A confirmation of the score update is sent back through the same path.
- The Transaction Server publishes the score update message to the Kafka Message Bus.
- The Kafka Message Bus broadcasts the score update to all relevant client applications, which then display the updated score.
### Real-Time Leaderboard:
- The Transaction Server updates the leaderboard in Redis as scores change and broadcasts these updates via Kafka.
- Clients receive prompt leaderboard updates through WebSockets to display the current standings.
