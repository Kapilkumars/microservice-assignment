# microservice-assignment

1. User Registration
	A new user can register by providing necessary details 
	User data is securely saved in the Auth/User Service.

2. User Login
	Registered users can log in using their credentials.
	Upon successful authentication, a JWT token is generated for secure communication.

3. Search Restaurants, Menus and Place order 
	Logged-in users can search for restaurants
	Restaurant data is fetched from the Restaurant Service via REST APIs.
	After selecting a restaurant, users can browse the menu items available.
	Menu items are dynamically loaded from the Menu Service.
	Users select menu items and place an order.
	On placing the order:
		The Order Service processes and saves the order details.
		Order details are pushed into RabbitMQ with QueueName = MicroserviceOrderQueue.

4. Order Cancellation
	Users can cancel the order at any time before it is marked as completed.
	On cancellation:
		The order ID is pushed into RabbitMQ with QueueName = MicroserviceCancelledOrderQueue.

5. Delivery Service (Consumer Service)
	This service consumes orders from RabbitMQ for processing deliveries.
	a. New Order Processing: Consumesmessages from MicroserviceOrderQueue.
		Searches for an available delivery partner from the User Service.
		Assigns a delivery partner and updates the delivery database with the order and partner details.
	
	b. Completion of Delivery: Once delivery is completed:
		Updates the Delivery DB status to Completed.
		Sends an HTTP request to Order Service to update the order status to Completed.
		Sends an HTTP request to Auth/User Service to mark the delivery partner’s isAvailable status as TRUE.

	c. Handling Cancelled Orders:
		Consumes cancellation messages from MicroserviceCancelledOrderQueue.
		Updates Auth/User Service to mark the delivery partner as isAvailable = TRUE.