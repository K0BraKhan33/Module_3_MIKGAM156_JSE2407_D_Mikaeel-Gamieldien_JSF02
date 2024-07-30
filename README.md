GoodMarting Svelte Project

GoodMarting is a Svelte-based e-commerce application showcasing a list of products with detailed views. The project features product filtering, sorting, and navigation functionalities, all built with Vite and Svelte.

Technologies Used

Vite: A modern build tool for fast development and optimized production builds.
Svelte: A component framework for building user interfaces.
Svelte-Spa-Router: A lightweight router for single-page applications in Svelte.
Fake Store API: A free API used to fetch product data.
Setup Instructions

Prerequisites

Ensure you have the following installed:

Node.js (v14 or higher)
Clone the Repository

bash:

Copy code-
git clone https://github.com/K0BraKhan33/Module_3_MIKGAM156_JSE2407_D_Mikaeel-Gamieldien_JSF02.git

cd Module_3_MIKGAM156_JSE2407_D_Mikaeel-Gamieldien_JSF02

Install Dependencies

bash:

Copy code
npm install
Start Development Server

bash:

Copy code
npm run dev
Navigate to http://localhost:5173 in your browser to see the application in action.

Project Structure

src/App.svelte: The main entry point for the application, setting up routing.
src/Components/ProductList.svelte: Displays a list of products with sorting and filtering options.
src/Components/ProductDetail.svelte: Shows detailed information about a single product.
src/main.js: Initializes and mounts the Svelte application.
vite.config.js: Configuration for Vite.
Features

Product Grid: Display products in a responsive grid layout.
Product Details: View detailed information about each product, including title, description, image, price, category, and rating.
Sorting: Sort products by price (ascending or descending) or title (alphabetical).
Filtering: Filter products by category.
Loading States: Inform users when data is being fetched.
Persistent Filters: Maintain applied filters and sorting when navigating between views.
Usage Examples

Navigating to Product Details

Click on a product card to navigate to its detailed view. The URL will include the product's ID and current sorting/filtering parameters.

Sorting and Filtering

Use the dropdowns at the top of the product list to sort and filter products. Changes will be reflected immediately and will persist across navigation.

Returning to the Product List

Use the back button in the detailed view to return to the product list while preserving your applied filters and sorting.

API Endpoints

Product List: https://fakestoreapi.com/products
Product Details: https://fakestoreapi.com/products/{id}
Categories: https://fakestoreapi.com/products/categories
Development Notes

The application does not use local storage for filters or sorting. State is managed via URL parameters.
Tailwind CSS is included via CDN for styling.
Contributing

Contributions are welcome! Please fork the repository and submit a pull request with your changes.

Host of Project: Mikaeel Gamieldien, [K0BraKhan33](https://github.com/K0BraKhan33)