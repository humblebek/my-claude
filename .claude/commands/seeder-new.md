# Seeder New Command

Generate database seeders with realistic test data.

## Purpose
Create seeders with factories for development and testing environments.

## Usage
```
/seeder-new
```

Then describe: "Seed 100 products with categories", "Create admin user", "Populate test data"

## Example Output

```php
// database/seeders/ProductSeeder.php
namespace Database\Seeders;

use App\Models\Category;
use App\Models\Product;
use App\Models\Tag;
use Illuminate\Database\Seeder;

class ProductSeeder extends Seeder
{
    public function run(): void
    {
        // Create categories first
        $categories = Category::factory(10)->create();
        
        // Create tags
        $tags = Tag::factory(20)->create();
        
        // Create products with relationships
        $categories->each(function ($category) use ($tags) {
            Product::factory(10)
                ->for($category)
                ->hasAttached($tags->random(3))
                ->create();
        });
        
        // Create some featured products
        Product::factory(5)
            ->for($categories->random())
            ->create(['is_featured' => true]);
    }
}
```

## Factory Example

```php
// database/factories/ProductFactory.php
namespace Database\Factories;

use App\Models\Category;
use Illuminate\Database\Eloquent\Factories\Factory;

class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->words(3, true),
            'slug' => fake()->unique()->slug(),
            'description' => fake()->paragraph(),
            'price' => fake()->randomFloat(2, 10, 1000),
            'stock' => fake()->numberBetween(0, 100),
            'category_id' => Category::factory(),
            'is_active' => fake()->boolean(80),
            'meta' => [
                'weight' => fake()->randomFloat(2, 0.1, 50),
                'dimensions' => [
                    'width' => fake()->numberBetween(10, 100),
                    'height' => fake()->numberBetween(10, 100),
                    'depth' => fake()->numberBetween(10, 100),
                ],
            ],
        ];
    }

    public function inactive(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_active' => false,
        ]);
    }

    public function outOfStock(): static
    {
        return $this->state(fn (array $attributes) => [
            'stock' => 0,
        ]);
    }
}
```

## Usage in DatabaseSeeder

```php
// database/seeders/DatabaseSeeder.php
public function run(): void
{
    $this->call([
        UserSeeder::class,
        CategorySeeder::class,
        ProductSeeder::class,
        OrderSeeder::class,
    ]);
}
```

## Running Seeders

```bash
# Run all seeders
php artisan db:seed

# Run specific seeder
php artisan db:seed --class=ProductSeeder

# Fresh migration with seeding
php artisan migrate:fresh --seed
```
