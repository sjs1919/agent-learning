# PHP 测试规范 (PHPUnit)

## 概述

本文档定义了 PHP 项目（Laravel 和原生 PHP）的测试规范，包括单元测试、功能测试和集成测试的最佳实践。

## 测试框架

### 1. PHPUnit

PHP 单元测试的标准框架，适用于所有 PHP 项目。

### 2. Laravel 测试工具

Laravel 项目使用 PHPUnit 的封装，提供更便捷的测试方式。

## 项目配置

### PHPUnit 配置文件 (`phpunit.xml`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.5/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         stopOnFailure="false"
         executionOrder="depends,defects"
         beStrictAboutOutputDuringTests="true"
         failOnRisky="true"
         failOnWarning="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
    <coverage>
        <include>
            <directory suffix=".php">./app</directory>
        </include>
        <report>
            <text outputFile="php://stdout" showUncoveredFiles="false"/>
        </report>
    </coverage>
</phpunit>
```

## 测试类型

### 1. 单元测试 (Unit Tests)

测试单个类或方法的功能，不依赖外部服务。

**目录结构**：
```
tests/Unit/
├── Services/
│   ├── OrderServiceTest.php
│   └── UserServiceTest.php
├── Models/
│   ├── UserTest.php
│   └── ProductTest.php
└── Helpers/
    └── StringHelperTest.php
```

**示例**：
```php
<?php

namespace Tests\Unit\Services;

use App\Services\OrderService;
use Tests\TestCase;

class OrderServiceTest extends TestCase
{
    private OrderService $orderService;

    protected function setUp(): void
    {
        parent::setUp();
        $this->orderService = new OrderService();
    }

    public function test_calculate_order_total(): void
    {
        $items = [
            ['price' => 100, 'quantity' => 2],
            ['price' => 50, 'quantity' => 1],
        ];

        $total = $this->orderService->calculateTotal($items);

        $this->assertEquals(250, $total);
    }

    public function test_empty_order_total_is_zero(): void
    {
        $total = $this->orderService->calculateTotal([]);

        $this->assertEquals(0, $total);
    }

    /** @dataProvider discountProvider */
    public function test_apply_discount(float $total, float $rate, float $expected): void
    {
        $result = $this->orderService->applyDiscount($total, $rate);

        $this->assertEquals($expected, $result);
    }

    public static function discountProvider(): array
    {
        return [
            [100, 0.1, 90],
            [100, 0.2, 80],
            [200, 0.15, 170],
        ];
    }
}
```

### 2. 功能测试 (Feature Tests)

测试完整的功能流程，包括 HTTP 请求、数据库操作等。

**目录结构**：
```
tests/Feature/
├── Auth/
│   ├── LoginTest.php
│   └── RegisterTest.php
├── Api/
│   ├── OrderControllerTest.php
│   └── ProductControllerTest.php
└── Web/
    └── HomeControllerTest.php
```

**示例**：
```php
<?php

namespace Tests\Feature\Api;

use App\Models\User;
use App\Models\Order;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class OrderControllerTest extends TestCase
{
    use RefreshDatabase;

    private User $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user = User::factory()->create();
    }

    public function test_can_list_orders(): void
    {
        Order::factory()->count(5)->create(['user_id' => $this->user->id]);

        $response = $this->actingAs($this->user)
            ->getJson('/api/orders');

        $response->assertStatus(200)
            ->assertJsonCount(5, 'data');
    }

    public function test_can_create_order(): void
    {
        $orderData = [
            'customer_name' => 'John Doe',
            'total_amount' => 100.00,
            'items' => [
                ['product_id' => 1, 'quantity' => 2, 'price' => 50.00],
            ],
        ];

        $response = $this->actingAs($this->user)
            ->postJson('/api/orders', $orderData);

        $response->assertStatus(201)
            ->assertJsonPath('data.customer_name', 'John Doe')
            ->assertJsonPath('data.total_amount', 100.00);

        $this->assertDatabaseHas('orders', [
            'customer_name' => 'John Doe',
            'total_amount' => 100.00,
        ]);
    }

    public function test_unauthenticated_cannot_access_orders(): void
    {
        $response = $this->getJson('/api/orders');

        $response->assertStatus(401);
    }
}
```

### 3. 集成测试 (Integration Tests)

测试多个组件或服务的集成。

**示例**：
```php
<?php

namespace Tests\Integration;

use App\Models\User;
use App\Services\OrderService;
use App\Services\NotificationService;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Support\Facades\Mail;
use Tests\TestCase;

class OrderIntegrationTest extends TestCase
{
    use RefreshDatabase;

    public function test_order_creation_sends_notification(): void
    {
        Mail::fake();

        $user = User::factory()->create();
        $orderService = app(OrderService::class);
        $notificationService = app(NotificationService::class);

        $order = $orderService->createOrder([
            'user_id' => $user->id,
            'total' => 100,
        ]);

        $notificationService->sendOrderConfirmation($order);

        Mail::assertSent(OrderConfirmationMail::class, function ($mail) use ($user) {
            return $mail->hasTo($user->email);
        });
    }
}
```

## 测试数据工厂

### Laravel Factories

```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class UserFactory extends Factory
{
    protected $model = User::class;

    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'password' => bcrypt('password'),
            'is_active' => true,
        ];
    }

    public function inactive(): self
    {
        return $this->state(fn (array $attributes) => [
            'is_active' => false,
        ]);
    }
}

// 使用
$user = User::factory()->create();
$users = User::factory()->count(10)->create();
$inactiveUser = User::factory()->inactive()->create();
```

## Mock 和 Stub

### 1. 接口 Mock

```php
public function test_payment_service_calls_gateway(): void
{
    $gateway = $this->createMock(PaymentGatewayInterface::class);
    $gateway->expects($this->once())
        ->method('charge')
        ->with(100.00)
        ->willReturn(true);

    $service = new PaymentService($gateway);
    $result = $service->processPayment(100.00);

    $this->assertTrue($result);
}
```

### 2. Facade Mock (Laravel)

```php
public function test_sending_email(): void
{
    Mail::fake();

    $user = User::factory()->create();
    $service = new NotificationService();
    $service->sendWelcomeEmail($user);

    Mail::assertSent(WelcomeEmail::class, function ($mail) use ($user) {
        return $mail->hasTo($user->email);
    });
}

public function test_sending_email_to_queue(): void
{
    Queue::fake();

    $user = User::factory()->create();
    dispatch(new SendWelcomeEmailJob($user));

    Queue::assertPushed(SendWelcomeEmailJob::class);
}
```

### 3. HTTP 客户端 Mock

```php
public function test_external_api_call(): void
{
    Http::fake([
        'api.example.com/*' => Http::response(['data' => 'test'], 200),
    ]);

    $response = Http::get('https://api.example.com/test');

    $this->assertEquals('test', $response['data']);
    Http::assertSent(function ($request) {
        return $request->url() === 'https://api.example.com/test';
    });
}
```

## 数据库测试

### 1. 使用 RefreshDatabase

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class OrderTest extends TestCase
{
    use RefreshDatabase;

    public function test_order_can_be_created(): void
    {
        $order = Order::factory()->create();

        $this->assertDatabaseHas('orders', [
            'id' => $order->id,
        ]);
    }
}
```

### 2. 使用 DatabaseMigrations

```php
use Illuminate\Foundation\Testing\DatabaseMigrations;

class MigrationTest extends TestCase
{
    use DatabaseMigrations;

    // 测试会运行所有迁移
}
```

### 3. 事务回滚（手动控制）

```php
public function test_with_transaction(): void
{
    DB::beginTransaction();

    try {
        // 测试代码
        $order = Order::create([...]);

        $this->assertDatabaseHas('orders', ['id' => $order->id]);
    } finally {
        DB::rollBack();
    }
}
```

## 测试覆盖率

### 配置覆盖率

```xml
<coverage processUncoveredFiles="true">
    <include>
        <directory suffix=".php">./app</directory>
    </include>
    <exclude>
        <directory>./app/Http/Controllers</directory>
        <directory>./app/Models</directory>
    </exclude>
    <report>
        <html outputDirectory="coverage/html"/>
        <clover outputFile="coverage/clover.xml"/>
    </report>
</coverage>
```

### 运行覆盖率测试

```bash
# 生成覆盖率报告
vendor/bin/phpunit --coverage-html coverage/html

# 仅运行覆盖率不生成报告
vendor/bin/phpunit --coverage-text

# 过滤特定目录
vendor/bin/phpunit --coverage-filter ./app/Services
```

## 运行测试

### 基本命令

```bash
# 运行所有测试
vendor/bin/phpunit

# 运行特定测试文件
vendor/bin/phpunit tests/Unit/OrderServiceTest.php

# 运行特定测试方法
vendor/bin/phpunit --filter test_calculate_total

# 仅运行单元测试
vendor/bin/phpunit --testsuite=Unit

# 仅运行功能测试
vendor/bin/phpunit --testsuite=Feature

# 显示详细输出
vendor/bin/phpunit --testdox

# 停止在第一次失败
vendor/bin/phpunit --stop-on-failure

# 显示详细的错误信息
vendor/bin/phpunit --display-errors
```

### Laravel Artisan 命令

```bash
# 运行所有测试
php artisan test

# 并行运行测试
php artisan test --parallel

# 使用特定数据库连接
php artisan test --database=mysql_testing
```

## 测试命名规范

### 测试类命名

```
格式: {类名}Test
示例: OrderServiceTest, UserControllerTest, LoginTest
```

### 测试方法命名

```
格式: test_{被测试的功能}
示例: test_calculate_total, test_can_create_user
```

### 断言命名

```
使用语义化的断言名称，便于理解测试意图
- $this->assertTrue($result) ✅
- $this->assertIsArray($data) ✅
```

## 最佳实践

### 1. 每个测试只验证一件事

```php
// ❌ 不好：验证多个行为
public function test_user_and_order(): void
{
    $user = User::factory()->create();
    $order = Order::factory()->create(['user_id' => $user->id]);

    $this->assertDatabaseHas('users', ['id' => $user->id]);
    $this->assertDatabaseHas('orders', ['id' => $order->id]);
}

// ✅ 好：单一行为
public function test_user_can_be_created(): void
{
    $user = User::factory()->create();

    $this->assertDatabaseHas('users', ['id' => $user->id]);
}

public function test_order_can_be_created(): void
{
    $order = Order::factory()->create();

    $this->assertDatabaseHas('orders', ['id' => $order->id]);
}
```

### 2. 使用 Arrange-Act-Assert 模式

```php
public function test_calculate_total_with_discount(): void
{
    // Arrange - 准备测试数据
    $items = [['price' => 100, 'quantity' => 2]];
    $discount = 0.1;

    // Act - 执行被测试的代码
    $total = $this->orderService->calculateTotal($items, $discount);

    // Assert - 验证结果
    $this->assertEquals(180, $total);
}
```

### 3. 使用数据提供者

```php
/** @dataProvider totalProvider */
public function test_calculate_total(array $items, float $expected): void
{
    $total = $this->orderService->calculateTotal($items);

    $this->assertEquals($expected, $total);
}

public static function totalProvider(): array
{
    return [
        'single item' => [[['price' => 100, 'quantity' => 1]], 100],
        'multiple items' => [[['price' => 100, 'quantity' => 2]], 200],
        'empty items' => [[], 0],
    ];
}
```

### 4. 避免在测试中使用真实外部服务

```php
// ❌ 不好：调用真实 API
public function test_send_sms(): void
{
    $service = new SmsService();
    $service->send('1234567890', 'Hello');
}

// ✅ 好：Mock API
public function test_send_sms(): void
{
    $client = $this->createMock(SmsClient::class);
    $client->expects($this->once())
        ->method('send')
        ->with('1234567890', 'Hello');

    $service = new SmsService($client);
    $service->send('1234567890', 'Hello');
}
```

## CI 集成

### GitHub Actions 示例

```yaml
- name: Run PHPUnit tests
  run: vendor/bin/phpunit

- name: Generate coverage report
  run: vendor/bin/phpunit --coverage-clover coverage.xml

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage.xml
```

## 常用断言

### PHPUnit 断言

| 断言 | 说明 |
|------|------|
| `assertTrue($condition)` | 验证为真 |
| `assertFalse($condition)` | 验证为假 |
| `assertEquals($expected, $actual)` | 验证相等 |
| `assertNotEquals($expected, $actual)` | 验证不相等 |
| `assertSame($expected, $actual)` | 验证类型和值都相等 |
| `assertCount($count, $array)` | 验证数组长度 |
| `assertArrayHasKey($key, $array)` | 验证数组存在键 |
| `assertContains($needle, $haystack)` | 验证包含值 |
| `assertInstanceOf($class, $object)` | 验证实例类型 |
| `expectException($exception)` | 预期抛出异常 |

### Laravel 额外断言

| 断言 | 说明 |
|------|------|
| `$response->assertStatus($code)` | 验证 HTTP 状态码 |
| `$response->assertJson($data)` | 验证 JSON 响应 |
| `$response->assertJsonPath($path, $value)` | 验证 JSON 路径值 |
| `$response->assertRedirect($uri)` | 验证重定向 |
| `$this->assertDatabaseHas($table, $data)` | 验证数据库记录存在 |
| `$this->assertDatabaseMissing($table, $data)` | 验证数据库记录不存在 |
| `$this->assertAuthenticated()` | 验证用户已认证 |
| `$this->assertGuest()` | 验证用户未认证 |

## 参考资料

- [PHPUnit 官方文档](https://phpunit.de/documentation.html)
- [Laravel 测试文档](https://laravel.com/docs/testing)
- [测试驱动开发 (TDD)](https://en.wikipedia.org/wiki/Test-driven_development)
