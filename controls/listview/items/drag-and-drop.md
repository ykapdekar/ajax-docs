---
title: Drag and Drop
page_title: Drag and Drop | UI for ASP.NET AJAX Documentation
description: Drag and Drop
slug: listview/items/drag-and-drop
tags: drag,and,drop
published: True
position: 0
---

# Drag and Drop



## 

__RadListView__ offers the Items Drag & Drop capability, allowing you to easily implement scenarios that require dragging and moving data items on the page. For an online sample project, please check the [RadListView Items Drag & Drop demo](http://demos.telerik.com/aspnet-ajax/listview/examples/itemdragdrop/defaultcs.aspx).

To enable Items Drag & Drop in RadListView:

1. Set __RadListView.ClientSettings.AllowItemsDragDrop="true"__.

1. Add a __RadListViewItemDragHandle__ control to your ItemTemplate / AlternatingItemTemplate.

1. Add a CSS marker on a data item container element (__.rlvI__ for ItemTemplate, __.rlvA__ for AlternatingItemTemplate)

>note When Items Drag & Drop is enabled, RadListView renders client scripts that facilitate the dragging of items on the page. To be able to grab and move items, you need to place a __RadListViewItemDragHandle__ control inside your ItemTemplate / AlternatingItemTemplate. The latter renders a visual drag handle that you can use to move the items.
>


````C#
	<telerik:RadListView ID="RadListView1" runat="server" DataSourceID="SqlDataSource1"
	    OnItemDrop="RadListView1_ItemDrop" ItemPlaceholderID="TrackContainer" DataKeyNames="TrackID"
	    ClientDataKeyNames="TrackID" EnableEmbeddedSkins="false" Skin="">
	    <ClientSettings AllowItemsDragDrop="true">
	        <ClientEvents OnItemDropping="trackDropping" />
	    </ClientSettings>
	    <LayoutTemplate>
	        <asp:PlaceHolder ID="TrackContainer" runat="server"></asp:PlaceHolder>
	    </LayoutTemplate> 
	
	    <ItemTemplate>
	        <div class="track rlvI">
	            <telerik:RadListViewItemDragHandle ID="RadListViewItemDragHandle1" runat="server"
	                ToolTip="Drag to organize" />
	            <div class="info">
	                <h3>
	                    <%# Eval("Track") %>&nbsp;<%# Eval("Title") %>
	                </h3>
	                <div class="artist">
	                    <%# Eval("Artist") %>
	                </div>
	                <div class="album">
	                    <%# Eval("Album") %>
	                    <%# Eval("Year").ToString() != "" ? "(" + Eval("Year").ToString() + ")" : "" %>
	                </div>
	            </div>
	        </div>
	    </ItemTemplate>
	</telerik:RadListView> 			
````



>note When you start moving your grabbed item, RadListView needs to create a dragged item HTML element that will be moved along with your mouse cursor. This dragged item is a replica of the HTML element representing your data item. Therefore, you need to specify the container element of your data items through the __.rlvI / .rlvA__ CSS markers. These are the same CSS classes applied to RadListView's data items when using[built-in RadListView Skins](http://www.telerik.com/help/aspnet-ajax/listview-skins.html).
>


On the server, RadListView fires the __ItemDrop__ event you can handle. The event argument passed to the handler is of type __RadListViewItemDragDropEventArgs__ and it exposes the __DraggedItem__ and __DestinationHtmlElement__ properties. The former references the RadListViewDataItem that has been dropped, while the latter is the client ID of the target HTML element, if any.

On the client-side, RadListView provides 4 client events you can use:

* __OnItemDragStarted__ - Fired when an item is about to be dragged (cancellable)

* __OnItemDragging__ - Fired when an item is dragged (on mouse move)

* __OnItemDropping__ - Fired when an item is dropping on a target element (cancellable)

* __OnItemDropped__ - Fired after OnItemDropping, before RadListView postbacks

All client events, except for OnItemDragging, pass the dragged item display index as well as any client data key values to the event handler through the second (__args__) parameter. Additionally, the OnItemDropping and OnItemDropped events also pass the destination HTML element inside the args object.

````JavaScript
	function trackDropping(sender, args)
	{
	    var dest = args.get_destinationElement();
	    if (!dest || !dest.id || dest.id.indexOf("GenreLink") < 0)
	    {
	        args.set_cancel(true);
	    }
	}			
````



As of version __Q2 2010 SP1__ of Telerik controls, RadListView supports custom drag handles. To implement a custom drag handle for your listview items, you should:

1. Add the __.rlvDrag__ CSS class to your drag handle element

1. Add the following __onmousedown__ event handler to the drag handle element:#_ASPX_

	Telerik.Web.UI.RadListView.HandleDrag(event, '[RadListViewClientID]', [ItemDisplayIndex])

where __[RadListViewClientID]__ is the ClientID of the RadListView instance, while __[ItemDisplayIndex]__ is the ListViewDataItem.DisplayIndex of the current item.

For example, if you have a generic __<div>__ container in your RadListView ItemTemplate that you want to turn into a drag handle, you can use the following markup:

````ASPNET
	<telerik:RadListView ID="RadListView1" runat="server">
	    <ItemTemplate>
	        <div class="rlvI">
	            <div class="rlvDrag" onmousedown="Telerik.Web.UI.RadListView.HandleDrag(event, '<%# Container.OwnerListView.ClientID %>', <%# Container.DisplayIndex%>)">
	            </div>
	            <div>
	    </ItemTemplate>
	</telerik:RadListView>
````



The above code demonstrates the usage of binding expressions to get the ClientID of the list view and DisplayIndex of the current item. The container with class name __.rlvDrag__ now can be used to drag the list view item. It is also possible to combine the __.rlvI__ and __.rlvDrag__ CSS classes into the topmost container to make the entire list view item draggable by clicking anywhere inside.