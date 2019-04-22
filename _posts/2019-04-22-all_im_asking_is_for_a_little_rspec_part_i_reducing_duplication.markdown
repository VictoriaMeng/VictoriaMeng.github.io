---
layout: post
title:      "All I'm Asking is for a Little RSpec, Part I: Reducing Duplication"
date:       2019-04-22 04:00:56 +0000
permalink:  all_im_asking_is_for_a_little_rspec_part_i_reducing_duplication
---

## The wrong ways to do it

With RSpec, there are three general ways to avoid duplication - initializing the same variables over and over again - in writing test units. The last way is better than the others.

### 1. Using  `before` with instance variables.

```
RSpec.describe Cat do
  before do
    @cat = Cat.new('Garfield', 'orange')
  end

  it 'has a name' do
    expect(@cat.name).to eq('Garfield')
  end

  it 'has a color' do
    expect(@cat.color).to eq('orange')
  end
end
```

The `before` block reinitializes the `@cat` instance variable before each example, but it's not efficient to initialize it over and over again. Furthermore, if the programmer uses the wrong instance variable name in the test, variable will be returned as `nil`, instead of throwing a more specific error message.

### 2. Helper methods

```
RSpec.describe Cat do
  def cat
	  Cat.new('Garfield', 'orange')
	end

  it 'has a name' do
    expect(cat.name).to eq('Garfield')
  end

  it 'has a color' do
    expect(cat.color).to eq('orange')
  end
end
```

The above code defines a local method `#cat` that creates a new instance of cat and returns it whenever it's called. The problem with using a helper method to reduce duplication is that you will get errors when trying to change the object's properties by calling the method with setters.

My next post will go into more detail on the third and final method.


