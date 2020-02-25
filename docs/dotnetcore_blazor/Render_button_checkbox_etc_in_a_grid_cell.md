## GridBlazor for ASP.NET Core MVC

# Render button, checkbox, etc. in a grid cell

[Index](Documentation.md)

The prefered method is using a Blazor component because it allows event handling with Blazor.
But you can also use the **RenderValueAs** method to render a custom html markup in the grid cell, as it is used on ASP.NET MVC Core projects.
In this case events will be managed using Javascript.

You have to use the **RenderComponentAs** method to render a component in a cell:

```c#
    columns.Add().RenderComponentAs<ButtonCell>();
```

**RenderComponentAs** method has 3 optional parameters:

Parameter | Type | Description
--------- | ---- | -----------
Actions | IList<Action<object>> (optional) | the parent component can pass a list of Actions to be used by the component (see [Passing grid state as parameter](Passing_grid_state_as_parameter.md))
Functions | IList<Func<object,Task>> (optional) | the parent component can pass a list of Functions to be used by the child component
Object | object (optional) | the parent component can pass an object to be used by the component (see [Passing grid state as parameter](Passing_grid_state_as_parameter.md))

If you use any of these paramenters, you must use them when creating the component.

The generic type used has to be the component created to render the cell.

You must also create a Blazor component that implements the **ICustomGridComponent** interface.
This interface includes a mandatory parameter called **Item** of the same type of the grid row element, and 4 optional parameters:

Parameter | Type | Description
--------- | ---- | -----------
Item | row element (mandatory) | the row item that will be used by the component
Grid | CGrid<T> (optional) | Grid can be used to get the grid state (see [Passing grid state as parameter](Passing_grid_state_as_parameter.md))
Actions | IList<Action<object>> (optional) | the parent component can pass a list of Actions to be used by the component (see [Passing grid state as parameter](Passing_grid_state_as_parameter.md))
Functions | IList<Func<object,Task>> (optional) | the parent component can pass a list of Functions to be used by the child component
Object | object (optional) | the parent component can pass an object to be used by the component (see [Passing grid state as parameter](Passing_grid_state_as_parameter.md))

**Actions**, **Functions** and **Object** must be used when calling the **RenderComponentAs** method, but **Grid** can be used without this requirement.
 
The component can include any html elements as well as any event handling features.

## Button

In this sample we name the component **ButtonCell.razor**:

```razor
    @implements ICustomGridComponent<Order>
    @inject IUriHelper UriHelper

    <button class='btn btn-sm btn-primary' @onclick="MyClickHandler">Edit</button>

    @code {
        [Parameter]
        public Order Item { get; protected set; }

        [Parameter]
        public IList<Action<object>> Actions { get; protected set; }

        [Parameter]
        public CGrid<Order> Grid { get; protected set; }

        [Parameter]
        public object Object { get; protected set; }

        private void MyClickHandler(UIMouseEventArgs e)
        {
            if (Actions == null)
            {
                string gridState = Grid.GetState();
                if (Object == null)
                {
                    UriHelper.NavigateTo($"/editorder/{Item.OrderID.ToString()}/gridsample/{gridState}");
                }
                else
                {
                    string returnUrl = (string)Object;
                    UriHelper.NavigateTo($"/editorder/{Item.OrderID.ToString()}/{returnUrl}/{gridState}");
                }
            }
            else
            {
                Actions[0]?.Invoke(Item);
            }
        }
    }
```

## Checkbox

```razor
    @using GridShared.Columns
    @implements ICustomGridComponent<Order>

    <input type='checkbox' @onchange="@CheckChanged" />

    @code {
        [Parameter]
        public Order Item { get; protected set; }

        private void CheckChanged()
        {
            Console.WriteLine("Check changed: Item " + Item.OrderID);
        }
    }
```

## Custom layout

```razor
    @using GridShared.Columns
    @implements ICustomGridComponent<Order>

    <b><a class='modal_link' href='#' @onclick="@MyClickHandler">Edit</a></b>

    @code {
        [Parameter]
        public Order Item { get; protected set; }

        private void MyClickHandler(UIMouseEventArgs e)
        {
            Console.WriteLine("Button clicked: /Home/Edit/" + Item.OrderID);
        }
    }
```
[<- Data annotations](Data_annotations.md) | [Subgrids ->](Subgrids.md)