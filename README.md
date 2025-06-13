# InventorySync-Service-
InventorySync Service - Developer Documentation 

## Overview  
**InventorySync** is a critical backend service responsible for maintaining real-time consistency between warehouse databases and the main inventory system. It ensures that stock levels, product availability, and location data are synchronized across all systems with minimal latency.  

### Key Responsibilities:  
- Listens to warehouse database change events (inserts/updates/deletes).  
- Processes and validates inventory updates.  
- Propagates changes to the central inventory system via REST APIs.  
- Handles retries and conflict resolution for failed syncs.  

## Environment Setup  

### Prerequisites  
- Java 17+ (or Node.js v18+ if applicable)  
- Docker & Docker Compose  
- PostgreSQL 14+ (for local testing)  
- Kafka (for event streaming)  

### Installation  
1. Clone the repository:  
   ```bash  
   git clone https://github.com/your-org/InventorySync.git  
   cd InventorySync  
   ```  

2. Set up dependencies:  
   ```bash  
   docker-compose up -d  # Starts Kafka, PostgreSQL, and mock warehouse DB  
   ```  

3. Configure environment variables (copy `.env.example` to `.env`):  
   ```ini  
   DB_URL=jdbc:postgresql://localhost:5432/inventorysync  
   KAFKA_BROKERS=localhost:9092  
   WAREHOUSE_API_ENDPOINT=http://localhost:8081/api/warehouse  
   ```  

## Running Tests  

### Unit Tests  
```bash  
./gradlew test  # Java  
# OR  
npm test       # Node.js  
```  

### Integration Tests  
Spin up test containers first:  
```bash  
docker-compose -f docker-compose.test.yml up -d  
./gradlew integrationTest  
```  

## Key API Endpoints  

### 1. Trigger Manual Sync  
`POST /sync/trigger`  
Manually initiates a sync cycle (useful for debugging).  

**Request:**  
```bash  
curl -X POST http://localhost:8080/sync/trigger \  
  -H "Authorization: Bearer <API_KEY>"  
```  

**Response:**  
```json  
{  
  "jobId": "sync-123",  
  "status": "queued"  
}  
```  

### 2. Check Sync Status  
`GET /sync/status/{jobId}`  

### 3. Force Warehouse Resync  
`POST /sync/warehouses/{warehouseId}`  

---  

## Data Sync Flow Diagram  
To be added

### Flow Description:  
1. Warehouse DB emits change events → Kafka  
2. InventorySync consumes events, validates business rules  
3. Updates are batched and sent to Central Inventory System  
4. Success/failure logged and metrics emitted  

## Troubleshooting  
- **"Event Stuck"**: Check Kafka consumer lag: `kafka-consumer-groups --describe`  
- **DB Conflicts**: Run `SELECT * FROM sync_conflicts` for reconciliation hints  

## Contact  
For urgent issues, tag `@team-inventorysync` in Slack. 
