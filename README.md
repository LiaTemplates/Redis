<!--
author: André Dietrich

email: LiaScript@web.de

version: 0.0.1

language: en

narrator: US English Female

comment: A LiaScript template for integrating Redis-Mock for in-browser Redis database demonstrations and interactive tutorials.

link: https://cdn.jsdelivr.net/chartist.js/latest/chartist.min.css

script: dist/index.js

@Redis.eval
<script>
setTimeout(async function() {
  try {
    @input
  } catch (error) {
    console.error("Error evaluating input -> ", error.message);
  }
  send.lia("")
}, 100)

"LIA: wait"
</script>
@end


@Redis.terminal
<script>
setTimeout(async function() {
@input

send.handle("input", async (command) => {
  try {
    const result = await eval(command);
    if (result !== undefined) {
      console.log(result);
    }
  } catch (error) {
    console.error("Error evaluating input:", error);
  }
});

}, 100)

"LIA: terminal"
</script>
@end


-->

# Redis

                --{{0}}--
Redis-Mock is a feature-complete in-memory mock implementation of Redis for Node.js, designed for testing and educational purposes. It allows you to work with Redis commands directly in the browser without requiring an actual Redis server installation. This LiaScript template brings Redis-Mock into your interactive courses, enabling students to experiment with Redis commands and database operations in a safe, sandboxed environment.

For more information about Redis-Mock, visit the [redis-mock repository](https://github.com/yeahoffline/redis-mock) or the [ioredis-mock repository](https://github.com/stipsan/ioredis-mock).

* **Try it on LiaScript:**

  https://liascript.github.io/course/?https://raw.githubusercontent.com/LiaTemplates/Redis/main/README.md

* **See the project on Github:**

  https://github.com/LiaTemplates/Redis

* **Experiment in the LiveEditor:**

  https://liascript.github.io/LiveEditor/?/show/file/https://raw.githubusercontent.com/LiaTemplates/Redis/main/README.md

                --{{1}}--
Like with other LiaScript templates, there are three ways to integrate Redis-Mock into your course. The easiest way is to copy the import statement into your project. For more information, see the Implementation section.

                  {{1}}
********************************************************************************

**Import Methods:**

1. Load the latest macros via (this might cause breaking changes):

   `import: https://raw.githubusercontent.com/LiaTemplates/Redis/main/README.md`

   or the current version 0.0.1 via:

   `import: https://raw.githubusercontent.com/LiaTemplates/Redis/0.0.1/README.md`

2. Copy the macro definitions into your project

3. Clone this repository on GitHub

********************************************************************************

## `@Redis.eval`

                --{{0}}--
This is the primary macro for executing Redis commands in LiaScript. It creates an in-memory Redis instance and executes your code, displaying the results in the console. All Redis operations are performed in-memory, making it perfect for demonstrations and interactive exercises without requiring backend infrastructure.

**Basic String Operations:**

```js
var redis = new Redis();

// Set and get a simple key-value pair
redis.set('greeting', 'Hello, Redis!');
redis.get('greeting').then(console.log);

// Set multiple values
redis.set('name', 'Alice');
redis.set('age', '30');
redis.set('city', 'Wonderland');

// Get and display all values
Promise.all([
  redis.get('name'),
  redis.get('age'),
  redis.get('city')
]).then(values => {
  console.log('Name:', values[0]);
  console.log('Age:', values[1]);
  console.log('City:', values[2]);
});
```
@Redis.eval

                --{{1}}--
Redis-Mock supports hash operations, which are perfect for storing objects and structured data. Hashes are ideal for representing objects like user profiles, product details, or any entity with multiple fields.

      {{1}}
**Hash Operations:**

```js
const redis = new Redis();

// Create a user profile using hashes
await redis.hset('user:1001', 'name', 'John Doe');
await redis.hset('user:1001', 'email', 'john@example.com');
await redis.hset('user:1001', 'age', '28');
await redis.hset('user:1001', 'role', 'developer');

// Retrieve the entire hash
const user = await redis.hgetall('user:1001');
console.log('User Profile:', user);

// Get specific fields
const name = await redis.hget('user:1001', 'name');
const email = await redis.hget('user:1001', 'email');
console.log(`${name} - ${email}`);

// Check if field exists
const hasPhone = await redis.hexists('user:1001', 'phone');
console.log('Has phone number:', hasPhone === 1 ? 'Yes' : 'No');
```
@Redis.eval

                --{{2}}--
Lists in Redis are ordered collections of strings, useful for implementing queues, stacks, or timelines. You can push and pop elements from both ends, making them versatile for many use cases.

      {{2}}
**List Operations:**

```js
const redis = new Redis();

// Create a task queue using lists
await redis.rpush('tasks', 'Write documentation');
await redis.rpush('tasks', 'Review pull requests');
await redis.rpush('tasks', 'Deploy to production');
await redis.rpush('tasks', 'Monitor logs');

// Get the length of the list
const length = await redis.llen('tasks');
console.log(`Total tasks: ${length}`);

// Get all tasks
const allTasks = await redis.lrange('tasks', 0, -1);
console.log('All tasks:', allTasks);

// Process tasks (pop from left)
const firstTask = await redis.lpop('tasks');
console.log('Processing:', firstTask);

// Get remaining tasks
const remaining = await redis.lrange('tasks', 0, -1);
console.log('Remaining tasks:', remaining);
```
@Redis.eval

                --{{3}}--
Sets are unordered collections of unique strings, perfect for storing tags, categories, or any scenario where you need to ensure uniqueness and perform set operations like unions and intersections.

      {{3}}
**Set Operations:**

```js
const redis = new Redis();

// Create sets of skills for different developers
await redis.sadd('dev:alice:skills', 'JavaScript', 'Python', 'Redis', 'Docker');
await redis.sadd('dev:bob:skills', 'Python', 'Java', 'Redis', 'Kubernetes');

// Get all members
const aliceSkills = await redis.smembers('dev:alice:skills');
const bobSkills = await redis.smembers('dev:bob:skills');
console.log('Alice knows:', aliceSkills);
console.log('Bob knows:', bobSkills);

// Check if a skill exists
const aliceKnowsRedis = await redis.sismember('dev:alice:skills', 'Redis');
console.log('Alice knows Redis:', aliceKnowsRedis === 1);

// Get set cardinality (size)
const aliceSkillCount = await redis.scard('dev:alice:skills');
console.log('Alice has', aliceSkillCount, 'skills');
```
@Redis.eval

                --{{4}}--
Sorted sets combine the uniqueness of sets with the ability to associate a score with each member, automatically keeping elements sorted by their scores. They're perfect for leaderboards, priority queues, and ranked data.

      {{4}}
**Sorted Set Operations (Leaderboard Example):**

```js
const redis = new Redis();

// Create a game leaderboard
await redis.zadd('leaderboard', 1500, 'Alice');
await redis.zadd('leaderboard', 2300, 'Bob');
await redis.zadd('leaderboard', 1800, 'Charlie');
await redis.zadd('leaderboard', 2100, 'Diana');
await redis.zadd('leaderboard', 1950, 'Eve');

// Get top 3 players (highest scores)
const topPlayers = await redis.zrevrange('leaderboard', 0, 2, 'WITHSCORES');
console.log('Top 3 Players:');
for (let i = 0; i < topPlayers.length; i += 2) {
  console.log(`${i/2 + 1}. ${topPlayers[i]} - ${topPlayers[i + 1]} points`);
}

// Get rank of a specific player (0-indexed, highest score = rank 0)
const aliceRank = await redis.zrevrank('leaderboard', 'Alice');
console.log(`Alice's rank: ${aliceRank + 1}`);

// Get score of a player
const bobScore = await redis.zscore('leaderboard', 'Bob');
console.log(`Bob's score: ${bobScore}`);

// Increment a player's score
await redis.zincrby('leaderboard', 200, 'Alice');
const newAliceScore = await redis.zscore('leaderboard', 'Alice');
console.log(`Alice's new score: ${newAliceScore}`);
```
@Redis.eval

### Pre-populated Database

                --{{0}}--
One of the powerful features of Redis-Mock is the ability to pre-populate the database with initial data. This is particularly useful for creating realistic demonstrations, tutorials, or testing scenarios where you need a database with existing data.

```js
const redis = new Redis({
  data: {
    // Simple key-value pairs
    'app:name': 'My Awesome App',
    'app:version': '1.0.0',
    
    // Hash data
    'user:1': { 
      id: '1', 
      username: 'superman', 
      email: 'clark@daily.planet',
      role: 'admin'
    },
    'user:2': { 
      id: '2', 
      username: 'batman', 
      email: 'bruce@wayne.enterprises',
      role: 'moderator'
    },
    
    // Lookup table
    emails: {
      'clark@daily.planet': '1',
      'bruce@wayne.enterprises': '2',
    },
    
    // Counter
    user_next: '3',
  },
});

// Now we can query the pre-populated data
const appName = await redis.get('app:name');
console.log('Application:', appName);

const user1 = await redis.hgetall('user:1');
console.log('User 1:', user1);

const clarkId = await redis.hget('emails', 'clark@daily.planet');
console.log('Clark Kent ID:', clarkId);

const nextId = await redis.get('user_next');
console.log('Next user ID will be:', nextId);
```
@Redis.eval

### Custom Commands with Lua

                --{{0}}--
Redis-Mock supports custom commands using Lua scripts, allowing you to create reusable, atomic operations. This is powerful for implementing complex business logic that needs to execute as a single atomic operation.

```js
const redis = new Redis({ data: { counter: '10', multiplier: '5' } });

// Define a custom command using Lua
const commandDefinition = {
  numberOfKeys: 1,
  lua: 'return redis.call("GET", KEYS[1]) * ARGV[1]',
};

redis.defineCommand('multiply', commandDefinition);

// Use the custom command
const result = await redis.multiply('counter', 3);
console.log('10 * 3 =', result);

// Another example: increment and get
redis.defineCommand('incrAndGet', {
  numberOfKeys: 1,
  lua: `
    redis.call("INCR", KEYS[1])
    return redis.call("GET", KEYS[1])
  `,
});

const newValue = await redis.incrAndGet('counter');
console.log('After increment:', newValue);

// Complex example: conditional update
redis.defineCommand('updateIfGreater', {
  numberOfKeys: 1,
  lua: `
    local current = tonumber(redis.call("GET", KEYS[1]))
    local newval = tonumber(ARGV[1])
    if newval > current then
      redis.call("SET", KEYS[1], newval)
      return 1
    end
    return 0
  `,
});

const updated = await redis.updateIfGreater('counter', 20);
console.log('Updated:', updated === 1 ? 'Yes' : 'No');

const finalValue = await redis.get('counter');
console.log('Final value:', finalValue);
```
@Redis.eval

### Multiple Redis Instances

                --{{0}}--
Redis-Mock allows you to create multiple isolated Redis instances, each listening on different ports (simulated). This is useful for demonstrating pub/sub patterns, master-slave configurations, or simply showing how different applications might connect to different Redis servers.

```js
// Create three Redis instances on different "ports"
const redis1 = new Redis(6380);
const redis2 = new Redis(6379); // Default port
const redis3 = new Redis(6380); // Same port as redis1

// Set data in redis1
await redis1.set('foo', 'bar');
await redis1.set('message', 'Hello from 6380');

// Set data in redis2 (different port)
await redis2.set('foo', 'baz');
await redis2.set('message', 'Hello from 6379');

// redis3 shares the same instance as redis1 (same port)
const value1 = await redis1.get('foo');
const value2 = await redis2.get('foo');
const value3 = await redis3.get('foo');

console.log('redis1 (6380):', value1); // 'bar'
console.log('redis2 (6379):', value2); // 'baz'
console.log('redis3 (6380):', value3); // 'bar' (same as redis1)

// Demonstrate isolation
const msg1 = await redis1.get('message');
const msg2 = await redis2.get('message');

console.log('\nMessages:');
console.log('Instance 6380:', msg1);
console.log('Instance 6379:', msg2);
```
@Redis.eval

### Expiration and TTL

                --{{0}}--
Redis supports key expiration, which is essential for implementing caching strategies, session management, and temporary data storage. You can set keys to automatically expire after a specified time.

```js
const redis = new Redis();

// Set a key with expiration (10 seconds)
await redis.set('session:abc123', 'user_data', 'EX', 10);

// Set a key and then set expiration separately
await redis.set('cache:data', 'cached_value');
await redis.expire('cache:data', 30);

// Check time-to-live
let ttl1 = await redis.ttl('session:abc123');
let ttl2 = await redis.ttl('cache:data');

console.log('session:abc123 TTL:', ttl1, 'seconds');
console.log('cache:data TTL:', ttl2, 'seconds');

// Set with SETEX (SET with EXpire)
await redis.setex('temp:token', 60, 'temporary_token_value');
const ttl3 = await redis.ttl('temp:token');
console.log('temp:token TTL:', ttl3, 'seconds');

// Check if keys exist
const exists1 = await redis.exists('session:abc123');
const exists2 = await redis.exists('nonexistent:key');
console.log('\nKey existence:');
console.log('session:abc123 exists:', exists1 === 1);
console.log('nonexistent:key exists:', exists2 === 1);
```
@Redis.eval

### Pattern Matching and Scanning

                --{{0}}--
Redis provides powerful pattern matching capabilities for finding keys. This is useful for querying, debugging, and managing keys in your database.

```js
const redis = new Redis();

// Create various keys with patterns
await redis.set('user:1:name', 'Alice');
await redis.set('user:1:email', 'alice@example.com');
await redis.set('user:2:name', 'Bob');
await redis.set('user:2:email', 'bob@example.com');
await redis.set('product:1:name', 'Laptop');
await redis.set('product:1:price', '999');
await redis.set('session:abc', 'active');
await redis.set('session:xyz', 'active');

// Find all user keys
const userKeys = await redis.keys('user:*');
console.log('All user keys:', userKeys);

// Find all user names
const userNames = await redis.keys('user:*:name');
console.log('All user name keys:', userNames);

// Find all keys for user 1
const user1Keys = await redis.keys('user:1:*');
console.log('User 1 keys:', user1Keys);

// Get all keys
const allKeys = await redis.keys('*');
console.log('\nAll keys in database:', allKeys);

// Get database size
const dbSize = await redis.dbsize();
console.log('Total keys:', dbSize);
```
@Redis.eval

### Transaction Support

                --{{0}}--
Redis supports transactions through the MULTI/EXEC commands, allowing you to execute multiple commands atomically. This ensures that either all commands succeed or none of them do.

```js
const redis = new Redis();

// Set initial balance
await redis.set('account:alice', '1000');
await redis.set('account:bob', '500');

console.log('Initial balances:');
console.log('Alice:', await redis.get('account:alice'));
console.log('Bob:', await redis.get('account:bob'));

// Perform a transaction: transfer 100 from Alice to Bob
const result = await redis.multi()
  .decrby('account:alice', 100)
  .incrby('account:bob', 100)
  .exec();

console.log('\nTransaction result:', result);

console.log('\nFinal balances:');
console.log('Alice:', await redis.get('account:alice'));
console.log('Bob:', await redis.get('account:bob'));

// Another example: atomic counter updates
await redis.set('page:views', '0');
await redis.set('page:likes', '0');

await redis.multi()
  .incr('page:views')
  .incr('page:views')
  .incr('page:views')
  .incr('page:likes')
  .exec();

console.log('\nPage statistics:');
console.log('Views:', await redis.get('page:views'));
console.log('Likes:', await redis.get('page:likes'));
```
@Redis.eval

## `@Redis.terminal`

                --{{0}}--
The `@Redis.terminal` macro creates an interactive terminal environment where JavaScript code can be evaluated interactively in a persistent context. Unlike `@Redis.eval`, which executes code once, the terminal maintains state across multiple command executions, allowing users to build up a database state step-by-step through interactive commands typed directly into the terminal interface.

This is particularly powerful for:
- **Interactive Learning**: Students can experiment with Redis commands at their own pace
- **Live Demonstrations**: Instructors can type commands in real-time during presentations
- **Debugging Sessions**: Test and verify Redis operations interactively
- **Progressive Tutorials**: Build complex data structures step by step

**How it works:**
1. The initial code block sets up the environment and creates persistent variables
2. A terminal interface appears below the code
3. Users can type JavaScript expressions that interact with the initialized Redis instance
4. Each command is evaluated in the same scope, maintaining state
5. Results are displayed in the console

**Basic Interactive Terminal:**

```js
// Initialize a Redis instance that persists across commands
const redis = new Redis();

// Set some initial data
await redis.set('counter', '0');
await redis.hset('user:session', 'id', 'abc123');
await redis.hset('user:session', 'username', 'demo_user');

console.log('Redis terminal initialized!');
console.log('Counter:', await redis.get('counter'));
console.log('\n📝 Try these commands in the terminal:');
console.log('  await redis.incr("counter")');
console.log('  await redis.get("counter")');
console.log('  await redis.hgetall("user:session")');
console.log('  await redis.hset("user:session", "status", "active")');
```
@Redis.terminal

                --{{1}}--
Here's a more advanced example showing how to create an interactive shopping cart system. The terminal allows you to add items, update quantities, view the cart, and calculate totals interactively.

      {{1}}
**Interactive Shopping Cart:**

```js
// Create a Redis instance for managing a shopping cart
const redis = new Redis();
const cartKey = 'cart:user123';

// Helper function to display cart contents
async function showCart() {
  const cart = await redis.hgetall(cartKey);
  const items = Object.keys(cart);
  
  if (items.length === 0) {
    console.log('🛒 Cart is empty');
    return;
  }
  
  console.log('🛒 Shopping Cart:');
  let total = 0;
  for (const item of items) {
    const quantity = parseInt(cart[item]);
    console.log(`  - ${item}: ${quantity}`);
    total += quantity;
  }
  console.log(`Total items: ${total}`);
}

// Helper function to add items
async function addItem(itemName, quantity = 1) {
  await redis.hincrby(cartKey, itemName, quantity);
  console.log(`✅ Added ${quantity}x ${itemName}`);
  await showCart();
}

// Helper function to remove item
async function removeItem(itemName) {
  await redis.hdel(cartKey, itemName);
  console.log(`🗑️  Removed ${itemName}`);
  await showCart();
}

// Helper function to clear cart
async function clearCart() {
  await redis.del(cartKey);
  console.log('🗑️  Cart cleared');
}

console.log('Shopping Cart System Ready! 🛍️\n');
console.log('Try these commands:');
console.log('  await addItem("Laptop", 1)');
console.log('  await addItem("Mouse", 2)');
console.log('  await addItem("Keyboard", 1)');
console.log('  await showCart()');
console.log('  await removeItem("Mouse")');
console.log('  await clearCart()');
```
@Redis.terminal

                --{{2}}--
This example demonstrates an interactive task queue system where you can add tasks with priorities, process them, and view statistics. It showcases how complex application logic can be built and tested interactively.

      {{2}}
**Interactive Task Queue System:**

```js
// Task queue management system
const redis = new Redis();

// Task counter for unique IDs
let taskCounter = 1;

// Add a task to the queue with priority
async function addTask(description, priority = 1) {
  const taskId = `task:${taskCounter++}`;
  const timestamp = Date.now();
  
  // Store task details in a hash
  await redis.hset(taskId, 'description', description);
  await redis.hset(taskId, 'priority', priority);
  await redis.hset(taskId, 'status', 'pending');
  await redis.hset(taskId, 'created', timestamp);
  
  // Add to priority queue (sorted set)
  await redis.zadd('task:queue', priority, taskId);
  
  // Add to pending list
  await redis.rpush('task:pending', taskId);
  
  console.log(`✅ Task added: "${description}" (Priority: ${priority})`);
  return taskId;
}

// Process the highest priority task
async function processNext() {
  // Get highest priority task (lowest score = highest priority)
  const tasks = await redis.zrange('task:queue', 0, 0);
  
  if (tasks.length === 0) {
    console.log('⚠️  No tasks in queue');
    return null;
  }
  
  const taskId = tasks[0];
  const taskData = await redis.hgetall(taskId);
  
  // Update status
  await redis.hset(taskId, 'status', 'completed');
  await redis.hset(taskId, 'completed', Date.now());
  
  // Remove from queue
  await redis.zrem('task:queue', taskId);
  await redis.lrem('task:pending', 0, taskId);
  await redis.rpush('task:completed', taskId);
  
  console.log(`✓ Processed: "${taskData.description}"`);
  return taskId;
}

// View all pending tasks
async function viewQueue() {
  const taskIds = await redis.zrange('task:queue', 0, -1, 'WITHSCORES');
  
  if (taskIds.length === 0) {
    console.log('📋 Queue is empty');
    return;
  }
  
  console.log('📋 Task Queue (by priority):');
  for (let i = 0; i < taskIds.length; i += 2) {
    const taskId = taskIds[i];
    const priority = taskIds[i + 1];
    const task = await redis.hgetall(taskId);
    console.log(`  [${priority}] ${task.description} (${task.status})`);
  }
}

// Get statistics
async function getStats() {
  const pending = await redis.llen('task:pending');
  const completed = await redis.llen('task:completed');
  console.log('📊 Statistics:');
  console.log(`  Pending: ${pending}`);
  console.log(`  Completed: ${completed}`);
  console.log(`  Total: ${pending + completed}`);
}

console.log('Task Queue System Ready! 📋\n');
console.log('Available commands:');
console.log('  await addTask("Write documentation", 1)');
console.log('  await addTask("Fix bug in login", 0)  // Higher priority');
console.log('  await addTask("Update dependencies", 2)');
console.log('  await viewQueue()');
console.log('  await processNext()');
console.log('  await getStats()');
```
@Redis.terminal

                --{{3}}--
The terminal can also be used for teaching session management and authentication patterns. This example shows how to create, validate, and manage user sessions interactively.

      {{3}}
**Interactive Session Manager:**

```js
// Session management system
const redis = new Redis();
const SESSION_EXPIRY = 300; // 5 minutes in seconds

// Generate a simple session ID
function generateSessionId() {
  return 'sess_' + Math.random().toString(36).substr(2, 9);
}

// Create a new session for a user
async function createSession(username, userData = {}) {
  const sessionId = generateSessionId();
  const sessionKey = `session:${sessionId}`;
  
  // Store session data
  await redis.hset(sessionKey, 'username', username);
  await redis.hset(sessionKey, 'created', Date.now());
  
  // Store additional user data
  for (const [key, value] of Object.entries(userData)) {
    await redis.hset(sessionKey, key, JSON.stringify(value));
  }
  
  // Set expiration
  await redis.expire(sessionKey, SESSION_EXPIRY);
  
  // Map username to session
  await redis.set(`user:session:${username}`, sessionId);
  await redis.expire(`user:session:${username}`, SESSION_EXPIRY);
  
  // Add to active sessions set
  await redis.sadd('active:sessions', sessionId);
  
  console.log(`✅ Session created for ${username}`);
  console.log(`   Session ID: ${sessionId}`);
  console.log(`   Expires in: ${SESSION_EXPIRY} seconds`);
  
  return sessionId;
}

// Get session data
async function getSession(sessionId) {
  const sessionKey = `session:${sessionId}`;
  const exists = await redis.exists(sessionKey);
  
  if (!exists) {
    console.log('❌ Session not found or expired');
    return null;
  }
  
  const session = await redis.hgetall(sessionKey);
  const ttl = await redis.ttl(sessionKey);
  
  console.log('📋 Session Data:');
  console.log(`   Username: ${session.username}`);
  console.log(`   Created: ${new Date(parseInt(session.created)).toLocaleString()}`);
  console.log(`   TTL: ${ttl} seconds`);
  
  return session;
}

// Refresh session (extend expiration)
async function refreshSession(sessionId) {
  const sessionKey = `session:${sessionId}`;
  const result = await redis.expire(sessionKey, SESSION_EXPIRY);
  
  if (result === 1) {
    console.log(`✅ Session refreshed: ${sessionId}`);
  } else {
    console.log(`❌ Session not found: ${sessionId}`);
  }
  
  return result === 1;
}

// Destroy session (logout)
async function destroySession(sessionId) {
  const sessionKey = `session:${sessionId}`;
  const session = await redis.hgetall(sessionKey);
  
  if (session.username) {
    await redis.del(`user:session:${session.username}`);
  }
  
  await redis.del(sessionKey);
  await redis.srem('active:sessions', sessionId);
  
  console.log(`🗑️  Session destroyed: ${sessionId}`);
}

// List all active sessions
async function listActiveSessions() {
  const sessions = await redis.smembers('active:sessions');
  
  if (sessions.length === 0) {
    console.log('No active sessions');
    return;
  }
  
  console.log(`👥 Active Sessions (${sessions.length}):`);
  for (const sessionId of sessions) {
    const session = await redis.hgetall(`session:${sessionId}`);
    if (session.username) {
      const ttl = await redis.ttl(`session:${sessionId}`);
      console.log(`   ${session.username} - ${sessionId} (TTL: ${ttl}s)`);
    }
  }
}

console.log('Session Manager Ready! 🔐\n');
console.log('Try these commands:');
console.log('  const sid = await createSession("alice", { role: "admin" })');
console.log('  await getSession(sid)');
console.log('  await refreshSession(sid)');
console.log('  await createSession("bob", { role: "user" })');
console.log('  await listActiveSessions()');
console.log('  await destroySession(sid)');
```
@Redis.terminal

## Supported Redis Commands

                --{{0}}--
Redis-Mock implements a comprehensive set of Redis commands. Below is an overview of the supported operations:

**Keys:**
`del`, `keys`, `scan`, `exists`, `type`, `expire`, `ttl`, `incr`, `incrby`, `incrbyfloat`, `decr`, `decrby`, `rename`, `renamenx`, `dbsize`

**Strings:**
`get`, `set`, `append`, `getset`, `mget`, `mset`, `msetnx`, `setex`, `setnx`, `ping`

**Hashes:**
`hset`, `hsetnx`, `hget`, `hexists`, `hdel`, `hlen`, `hgetall`, `hscan`, `hmset`, `hmget`, `hkeys`, `hvals`, `hincrby`, `hincrbyfloat`

**Lists:**
`llen`, `lpush`, `rpush`, `lpushx`, `rpushx`, `lpop`, `rpop`, `blpop`, `brpop`, `lindex`, `lrange`, `lrem`, `lset`

**Sets:**
`sadd`, `srem`, `smembers`, `scard`, `sismember`, `sscan`

**Sorted Sets:**
`zadd`, `zcard`, `zcount`, `zincrby`, `zrange`, `zrangebyscore`, `zrank`, `zrem`, `zremrangebyrank`, `zremrangebyscore`, `zrevrange`, `zrevrangebyscore`, `zrevrank`, `zscore`, `zunionstore`, `zinterstore`

**Transactions:**
`multi`, `exec`, `discard`

**Server:**
`flushdb`, `flushall`, `time`

**Pub/Sub:**
`publish`, `subscribe`, `unsubscribe`, `psubscribe`, `punsubscribe`

## Implementation

                --{{0}}--
The LiaScript implementation of Redis-Mock is based on the ioredis-mock library, which provides a feature-complete mock of Redis that runs entirely in the browser. The template loads the Redis constructor globally, making it available in all code blocks.

See the implementation in [src/index.ts](https://github.com/LiaTemplates/Redis/blob/main/src/index.ts) for more details.

**Macro Definitions:**

```markdown
script: dist/index.js

@Redis.eval
<script>
setTimeout(async function() {
  try {
    @input
  } catch (error) {
    console.error("Error evaluating input -> ", error.message);
  }
  send.lia("")
}, 100)

"LIA: wait"
</script>
@end

@Redis.terminal
<script>
setTimeout(async function() {
@input

async function evaluateInScope(code) {
  try {
    return await eval(`(async () => { ${code} })()`);
  } catch (err) {
    throw err;
  }
}

send.handle("input", async (command) => {
  try {
    const result = await evaluateInScope(command);
    console.log(result);
  } catch (error) {
    console.error("Error evaluating input:", error);
  }
});

}, 100)

"LIA: terminal"
</script>
@end
```

## Use Cases

                --{{0}}--
This template is perfect for various educational and demonstration scenarios:

* **Database Courses**: Teach Redis concepts without requiring server setup
* **Caching Strategies**: Demonstrate caching patterns and best practices
* **Data Structures**: Interactive exploration of Redis data types
* **Backend Development**: Show how applications interact with Redis
* **System Design**: Illustrate distributed system patterns using Redis
* **Testing Tutorials**: Teach test-driven development with mocked databases

## Resources

* **Redis Documentation**: https://redis.io/documentation
* **redis-mock**: https://github.com/yeahoffline/redis-mock
* **ioredis-mock**: https://github.com/stipsan/ioredis-mock
* **LiaScript**: https://liascript.github.io

## License

This template is released under the MIT License. The redis-mock and ioredis-mock libraries are also MIT licensed.