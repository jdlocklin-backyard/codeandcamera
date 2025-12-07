
##Tech

## Heading Level 2
### Heading Level 3
#### Heading Level 4
##### Heading Level 5
###### Heading Level 6


### Colors

| Color Name | Hex Code | Sample |
|------------|----------|---------|
| Red        | #FF0000  | <span style="color:#FF0000">■■■</span> |
| Blue       | #0000FF  | <span style="color:#0000FF">■■■</span> |
| Green      | #00FF00  | <span style="color:#00FF00">■■■</span> |
| Yellow     | #FFFF00  | <span style="color:#FFFF00">■■■</span> |
| Purple     | #800080  | <span style="color:#800080">■■■</span> |
| Orange     | #FFA500  | <span style="color:#FFA500">■■■</span> |
| Pink       | #FFC0CB  | <span style="color:#FFC0CB">■■■</span> |
| Teal       | #008080  | <span style="color:#008080">■■■</span> |
| Brown      | #A52A2A  | <span style="color:#A52A2A">■■■</span> |
| Navy       | #000080  | <span style="color:#000080">■■■</span> |




###Code

``` py
import tensorflow as tf
```

``` yaml
theme:
  features:
    - content.code.annotate # (1)
```


```py title="add_numbers.py" 
# Function to add two numbers
def add_two_numbers(num1, num2):
    return num1 + num2

# Example usage
result = add_two_numbers(5, 3)
print('The sum is:', result)
```


###Callouts

!!! note "Callout"

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.




!!! note ""

    Callout without title.  Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.


???+ "Collapse Call out"

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.





### Links

[OpenAI](https://www.openai.com)

[Local Link](./gardening/markdown_examples.md)




## Content Tabs

Examples of content tabs.

### Generic Content

=== "Plain text"

    This is some plain text

=== "Unordered list"

    * First item
    * Second item
    * Third item

=== "Ordered list"

    1. First item
    2. Second item
    3. Third item


### Code Blocks in Content Tabs

=== "Python"

    ```py
    def main():
        print("Hello world!")

    if __name__ == "__main__":
        main()
    ```

=== "JavaScript"

    ```js
    function main() {
        console.log("Hello world!");
    }

    main();
    ```