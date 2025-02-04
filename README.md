# README: Chat Server with Groups and Private Messages

## Assignment Features

### Implemented Features:
- Multi-threaded TCP-based server
- User authentication using `users.txt`
- Private messaging between users
- Broadcasting messages to all users
- Group creation and management (join, leave, and group messaging)
- Thread-safe data access using `std::mutex`

### Not Implemented Features:
- Persistent storage for messages or groups after server restarts
- Message history retrieval

---

## Design Decisions

### Threading Model:
- A new thread is created for each client connection.
- This ensures parallel handling of multiple clients without blocking the server.
- `std::thread` is used with `detach()` to allow independent execution.

### Synchronization:
- `std::mutex` is used to protect shared resources:
  - `clientsMutex` for the `clients` map (tracking connected users)
  - `groupsMutex` for the `groups` map (managing group memberships)
  - `serverMutex` for server-side console output
- `std::lock_guard<std::mutex>` ensures thread safety.

### Data Structures Used:
- `std::unordered_map<std::string, int>` → Maps username to socket descriptor
- `std::unordered_map<std::string, std::unordered_set<int>>` → Maps group names to connected clients
- `std::unordered_map<int, std::unordered_set<std::string>>` → Maps client sockets to joined groups
- `std::unordered_map<std::string, std::string>` → Stores user credentials

---

## Implementation

### High-Level Idea:
- The server listens on a predefined port (`12345`).
- Each client request is handled in a separate thread.
- Commands from clients are parsed and processed accordingly.

### Code Flow:
1. **Server Initialization:**
   - Reads `users.txt` and loads user credentials into memory.
   - Binds to a socket and listens for connections.
2. **Client Connection Handling:**
   - Prompts for authentication (username/password check).
   - If authentication fails, the client is disconnected.
   - If authenticated, the user is added to the `clients` map.
3. **Command Processing:**
   - `/msg <username> <message>` → Sends a private message.
   - `/broadcast <message>` → Sends a message to all users.
   - `/create_group <group>` → Creates a new group.
   - `/join_group <group>` → Adds the user to a group.
   - `/leave_group <group>` → Removes the user from a group.
   - `/group_msg <group> <message>` → Sends a message to a group.
4. **Client Disconnection:**
   - Removes user from `clients` map.
   - Removes user from all groups.
   - Closes the socket connection.

---

## Testing

### Correctness Testing:
- Verified basic authentication using valid and invalid credentials.
- Tested command parsing and execution for all required commands.

### Stress Testing:
- Simulated multiple concurrent clients.
- Sent large messages to check buffer handling.
- Tested rapid connection and disconnection.

---

## Server Restrictions
- Maximum concurrent clients: Limited by system resources.
- Maximum groups: No hard limit (depends on available memory).
- Maximum members per group: No hard limit (depends on memory and performance constraints).
- Maximum message size: `1024` bytes (defined by `MAX_BUFFER_SIZE`).

---

## Challenges Faced
1. **Authentication Handling:**
   - Initially, multiple logins for the same user were allowed.
   - Fixed by ensuring a username can only be connected once at a time.
2. **Thread Safety Issues:**
   - Encountered race conditions when updating shared data.
   - Resolved using proper `std::mutex` locks.
3. **Command Parsing Bugs:**
   - Early versions had issues correctly extracting arguments.
   - Fixed by improving string parsing logic.

---

## Declaration
We, **Sunandini Bansal, Havi Bohra, and Keval**, declare that this implementation is our own work and does not involve plagiarism. All referenced materials were used for learning purposes only.

---

## Feedback
- The assignment was well-structured and provided a solid understanding of socket programming.
- Clearer specifications on handling edge cases (e.g., what happens if a user tries to send a message to themselves) would be helpful.
- Providing example test cases would improve debugging efficiency.

