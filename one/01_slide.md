!SLIDE 

# Unit testing #


!SLIDE big

# TDD

!SLIDE

# Just kidding ...

!SLIDE bullets

## Just a few quick tips / techniques

!SLIDE

# Assertions

!SLIDE code smaller

# Specific Assertions

    @@@ php

    $this->assertTrue(count($collection) == 17);
    // outputs "asserting that false != true failed."

    // versus:

    $this->assertEqual(17, count($collection));
    // outputs "asserting that 422 matches expected 17 failed."

!SLIDE

* Feedback in Jenkins' console output.
* Avoids Debug::dump, i.e. less noise

!SLIDE

# Custom Assertions

* app/protected/class/tests/Test.php
* expressive, intention revealing
* code reuse 

!SLIDE code smaller

# Examples

    @@@ php

    // assertions we already have
    $this->assertIsArrayWithKeys()
    $this->assertPattern()

    // but maybe also?
    $this->assertSonoraMessageWasSent($fn)
    $this->assertNoDebugError()


!SLIDE

# Breaking up tests

!SLIDE code smaller

    @@@ php

    public function testThisStuffWorks() {
      $data = array(
        stuff,
        stuff,
        ...
      );

      foreach( $data as $thingy => $expected) {
        $this->assertEqual($expected, somethingWith($thingy) );
      }
    }

!SLIDE code smaller

    @@@ php

    public function testCreateThingyWorks() {
      $thingy = array();
      $this->assertEqual("expected", create($thingy));
    }

    public function testUpdateThingyWorks() {
      $thingy = array();
      $this->assertEqual("expected", update($thingy));
    }

    public function etcetcetc

!SLIDE bullets 

* clear feedback when test fails
* step 7 of 14 fails, 8 -> 14 won't run
* speed is measured per test


!SLIDE

# Things to avoid

!SLIDE

## Depending on state from previous tests.

e.g. test A asserts some static property is empty, test B sets it. Then order changes.

!SLIDE

## Depending on state from previous test runs.

recreate state of the world in the test or in setUp() 

( not always possible of course, e.g. Account )

!SLIDE bullets

# Faster tests: test doubles

( forget phpunit mocking )

!SLIDE code smaller

# Class has a super expensive method

    @@@ php

    class Something {
      public static function getSomethingVeryHeavyFromDatabase() {
        return DB::get()-> .......
      }

      public static function methodThatIWantToTest() {
        $x = getSomethingVeryHeavyFromDatabase();
        $ret = dosomethingWith($x);
        return $ret;
      }
    }

!SLIDE code smaller

    @@@ php

    class FakeSomething extends Something {
      public static function getSomethingVeryHeavyFromDatabase() {
        return array (1, 2, 3);
      }
    }

    public SomethingTest extends Tests_Test {
      public static function testMethodIWantToTest() {
        FakeSomething::methodIWantToTest();
      }
    }

!SLIDE bullets

## protected over private makes this easier


!SLIDE bullets

# Dependency injection

!SLIDE code smaller

    @@@ php

    class Something {
      public function get() {
        DB::get()-> .....
      }

      public function getAll() {
        DB::get()-> ...
      }
    }

!SLIDE code smaller

    @@@ php

    class SomethingStore {
      public function get() {
        DB::get()->
      }

      public function getAll() {
        DB::get()->
      }
    }

    class Something {
      public function __construct($store = null) {
        if (is_null($store)) {
          $this->_store = new SomethingStore();
        } else {
          $this->_store = $store;
        }
      }

      public function get() {
        return $this->_store->get();
      }
    }

!SLIDE code smaller

    @@@ php

    class FakeSomethingStore {
      public function get() {
        return array(1,2,3);
      }
    }

    public function testSomething() {
      $subject = new Something(new FakeSomethingStore());
      $this->assert.....
    }


!SLIDE center
![bob](bob.jpg)
