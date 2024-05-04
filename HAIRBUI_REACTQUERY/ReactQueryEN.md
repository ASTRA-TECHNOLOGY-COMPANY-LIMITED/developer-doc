# @tanstack/react-query

## Table of contents

- [@tanstack/react-query](#tanstackreact-query)
	- [Table of contents](#table-of-contents)
	- [Introduction](#introduction)
		- [What is React Query and Why?](#what-is-react-query-and-why)
			- [What is React Query?](#what-is-react-query)
			- [Why React Query helpful?](#why-react-query-helpful)
			- [Comparing with Redux](#comparing-with-redux)
		- [Query Fundamentals](#query-fundamentals)
		- [Deduplication](#deduplication)
		- [Query Lifecycle](#query-lifecycle)
	- [Basic Usages](#basic-usages)
		- [Fetching data](#fetching-data)
		- [Fetching data with parameter](#fetching-data-with-parameter)
		- [Fetching on demand](#fetching-on-demand)
	- [Advanced Usage](#advanced-usage)
		- [Polling](#polling)
		- [Dependent Queries](#dependent-queries)
		- [Pagination](#pagination)

## Introduction

### What is React Query and Why?

#### What is React Query?

> Tanstack React Query is a library for React, which can help developers to get and update data easier. With the ability to fetch data from servers and update without a lot of code, React Query has been one of the fastest growing and most popular third party libraries in the React ecosystem.

#### Why React Query helpful?

1. Automatically caches data, so if you fetch the same data again, it's super fast since it comes from the cache instead of the server.

2. Easy to manage loading and error states, so your UI can show nice loading spinners or error messages without extra code.

3. Easy to invalidate and refetch data when it might be out of date.

4. Has deduping built in, so if you try to fetch the same data multiple times before the first fetch completes, it dedupes them into one request.

5. Works great with React hooks and integrates nicely into React apps.

#### Comparing with Redux

![Comparing with Redux](https://res.cloudinary.com/dmbz4r0vl/image/upload/v1714755860/Screenshot_2024-05-01_231847_rq7btb.png)

- Started developing 2 years ago, 2 years later than Redux
- Has more stars than Redux
- Redux is used to manage client state, however, React Query is only used to manage data from server
- Easy to use and manage than Redux

### Query Fundamentals

React Query provides some fundamental concepts that make data fetching and caching easier. Here are the key fundamentals:

1. Queries - A query is an asynchronous request to fetch data, like from an API. You create queries using the useQuery hook.

2. Query Keys - Each query is identified by a unique key, usually formed from the API endpoint and any variables needed.

3. Stale Data - React Query tracks if data is "stale" or potentially out-of-date and needs refetching. You control when data is considered stale.

4. Query Invalidation - You can manually invalidate cached data for a query to force a refetch from the server.

5. Mutations - For updating or editing server data, you create mutations using the useMutation hook.

6. Query Observers - Components can "observe" or subscribe to a query and its state (loading, error, data) using hooks or render props.

7. Background Refetching - React Query can automatically refetch stale data in the background while components keep using cached data.

8. Parallel Queries - Multiple queries can be fetched in parallel without conflicts or race conditions.

9. Query Deduping - If the same query is fired twice before completing, React Query dedupes them to a single request.

### Deduplication

> Deduplication in React Query refers to the ability to automatically prevent duplicate requests from being sent to the server when the same query is fired multiple times before the initial request completes.

Here's a simple example to illustrate how deduplication works:

![Deduplication](https://res.cloudinary.com/dmbz4r0vl/image/upload/v1714756278/abc.drawio_u4lve4.png)

1. First, fire a query to fetch data from /api/products.

2. Before that request completes, the same /api/products query is fired again, perhaps from another part of your app.

3. React Query detects that an identical query is already in-flight.

4. Instead of sending another request, React Query deduplicates the duplicate request.

5. When the initial request completes, the data is shared with both queries.

### Query Lifecycle

![Query lifecycle](https://res.cloudinary.com/dmbz4r0vl/image/upload/v1714756330/abc.drawio_1_jj1zts.png)

- Creation: This is a state occurs when developers create a query and haven't fired yet

- Fetching: State when data is fetching from server

- Fresh: State when data is fetched from server and ready to used. However, this state doesn't last for a long time because at the next second, the data is maybe out of date.

- Stale: Represents the out of date data that the app is currently using. Frontend needs to trigger fetching data again so that the data can be in the fresh state.

- Inactive: React Query has a garbage collector for managing cache on the browser, this state in some manner indicates to React query that if the data is not being used in the application, it will potentially be deleted after a while. This is a great feature of React Query, because we want to persist the recent information to not fetching data all the time and improve the speed/UX of our interfaces.

- Deleted: This happens when the data was inactive for a certain period of time and it's deleted from the cache. This timeout can be configurable locally for each query or globally.

## Basic Usages

### Fetching data

```javascript
const fetchCustomers = () =>
	fetch(`${API}/customer`).then((res) => res.json());

const BasicUsage = () => {
	const { data, isPending, isError, error } = useQuery({
		queryKey: ["customers"],
		queryFn: fetchCustomers,
	});

	useEffect(() => {
		if (isError) notification.error({ message: error.message });
	}, [isError, error]);

	if (isPending) return <Skeleton />;

	return (
		data && (
			<Row gutter={[16, 16]} style={{ padding: 16 }}>
				<Col span={20}>
					<Row gutter={[16, 16]}>
						{data.data.map((customer) => (
							<Col key={customer._id} span={4} flex>
								<CustomerCard customer={customer} />
							</Col>
						))}
					</Row>
				</Col>
			</Row>
		)
	);
};

export default BasicUsage;
```

### Fetching data with parameter

```javascript
const fetchCustomerDetail = (id) =>
	fetch(`${API}/customer/${id}`).then((res) => res.json());

const CustomerDetail = () => {
	const params = useParams();
	const { id } = params;

	const { data, isPending, isError, error } = useQuery({
		queryKey: ["customer", id],
		queryFn: () => fetchCustomerDetail(id),
	});

	useEffect(() => {
		if (isError) notification.error({ message: error.message });
	}, [isError, error]);

	if (isPending) return <Skeleton />;

	return (
		data && (
			<Space direction="vertical">
				<h1>{data.data.name}</h1>
				<Divider style={{ margin: 0 }} />
				<img
					src={`${API}/file/${data.data.avatar}`}
					alt={data.data.name}
					width={300}
				/>
				<p>{data.data.email}</p>
				<p>{data.data.address}</p>
				{data.data.tags.map((tag) => (
					<Tag key={tag._id}>{tag.name}</Tag>
				))}
			</Space>
		)
	);
};

export default CustomerDetail;
```

### Fetching on demand

```javascript
const fetchCustomers = () => fetch(`${API}/customer`).then((res) => res.json());

const FetchOnDemand = () => {
	const { mutate, data, isPending, isError, error } = useMutation({
		mutationFn: fetchCustomers,
	});

	useEffect(() => {
		if (isError) notification.error({ message: error.message });
	}, [isError, error]);

	if (isPending) return <Skeleton />;

	return (
		<Space direction="vertical">
			<Button type="primary" onClick={() => mutate()}>
				Load/Refresh
			</Button>
			{data && (
				<Row gutter={[16, 16]} style={{ padding: 16 }}>
					<Col span={20}>
						<Row gutter={[16, 16]}>
							{data.data.map((customer) => (
								<Col key={customer._id} span={4} flex>
									<CustomerCard customer={customer} />
								</Col>
							))}
						</Row>
					</Col>
				</Row>
			)}
		</Space>
	);
};

export default FetchOnDemand;
```

## Advanced Usage

### Polling

> In React Query, polling refers to the ability to automatically refetch data from the server at a specified interval. This can be useful for keeping data up-to-date with the latest changes from the server without requiring a manual refetch.

```javascript
function App() {
  const fetchTodos = async () => {
    const res = await fetch('/api/todos');
    return res.json();
  };

  const { isLoading, error, data } = useQuery('todos', fetchTodos, {
    refetchInterval: 5000,
  });
}
```

### Dependent Queries

> In React Query, dependent queries refer to the ability to fetch data for one query based on the data from another query. This allows you to create a chain or sequence of asynchronous data fetching operations that depend on the result of a previous query.

```javascript
const sleep = (n) => new Promise((resolve) => setTimeout(resolve, n));

const fetchCustomers = async (selectedTags) => {
	await sleep(5000);
	return fetch(
		`${API}/customer?` +
			new URLSearchParams(selectedTags.map((tag) => ["tags", tag]))
	).then((res) => res.json());
};

const fetchTags = () => fetch(`${API}/tag`).then((res) => res.json());

const DependentQueries = () => {
	const customersQuery = useQuery({
		queryKey: ["customers", selectedTags],
		queryFn: () => fetchCustomers(selectedTags),
	});

	const tagsQuery = useQuery({
		queryKey: ["tags"],
		queryFn: fetchTags,
		enabled: !!customersQuery,
	});

	if (customersQuery.isPending || tagsQuery.isPending) return <Skeleton />;

	return (
		customersQuery.data &&
		tagsQuery.data && (
			<Row gutter={[16, 16]} style={{ padding: 16 }}>
				<Col span={3}>
					<h2>Tags</h2>
					<Divider />
					<Space direction="vertical">
						{tagsQuery.data.data.map((tag) => (
							<Tag key={tag._id}>
								{tag.name}
							</Tag>
						))}
					</Space>
				</Col>
				<Col span={1}>
					<Divider type="vertical" style={{ height: "100%" }} />
				</Col>
				<Col span={20}>
					<Row gutter={[16, 16]}>
						{customersQuery.data.data.map((customer) => (
							<Col key={customer._id} span={4} flex>
								<CustomerCard customer={customer} />
							</Col>
						))}
					</Row>
				</Col>
			</Row>
		)
	);
};

export default DependentQueries;
```

### Pagination

```javascript
const fetchCustomers = (selectedTags, page, pageSize) =>
	fetch(
		`${API}/customer?` +
			new URLSearchParams(selectedTags.map((tag) => ["tags", tag])) +
			`&page=${page}&pageSize=${pageSize}`
	).then((res) => res.json());

const fetchTags = () => fetch(`${API}/tag`).then((res) => res.json());

const Paginate = () => {
	const [page, setPage] = useState(1);
	const [pageSize, setPageSize] = useState(100);
	const [selectedTags, setSelectedTags] = useState([]);

	const handleChange = (tag, checked) => {
		const nextSelectedTags = checked
			? [...selectedTags, tag]
			: selectedTags.filter((t) => t !== tag);
		setSelectedTags(nextSelectedTags);
	};

	const customersQuery = useQuery({
		queryKey: ["customers", selectedTags, page, pageSize],
		queryFn: () => fetchCustomers(selectedTags, page, pageSize),
	});

	const tagsQuery = useQuery({
		queryKey: ["tags"],
		queryFn: fetchTags,
	});

	useEffect(() => {
		if (customersQuery.isError)
			notification.error({ message: customersQuery.error.message });
		if (tagsQuery.isError)
			notification.error({ message: tagsQuery.error.message });
	}, [
		customersQuery.isError,
		customersQuery.error,
		tagsQuery.isError,
		tagsQuery.error,
	]);

	if (customersQuery.isPending || tagsQuery.isPending) return <Skeleton />;

	return (
		customersQuery.data &&
		tagsQuery.data && (
			<Row gutter={[16, 16]} style={{ padding: 16 }}>
				<Col span={3}>
					<h2>Tags</h2>
					<Divider />
					<Space direction="vertical">
						{tagsQuery.data.data.map((tag) => (
							<Tag.CheckableTag
								key={tag._id}
								style={{ width: "100%" }}
								checked={selectedTags.includes(tag.name)}
								onChange={(checked) => handleChange(tag.name, checked)}
							>
								{tag.name}
							</Tag.CheckableTag>
						))}
					</Space>
				</Col>
				<Col span={1}>
					<Divider type="vertical" style={{ height: "100%" }} />
				</Col>
				<Col span={20}>
					<Row gutter={[16, 16]}>
						<Col span={24}>
							<Pagination
								style={{ right: 0 }}
								showQuickJumper
								showTotal={(total, range) =>
									`${range[0]}-${range[1]} of ${total} items`
								}
								defaultCurrent={page}
								total={customersQuery.data.data.total}
								defaultPageSize={pageSize}
								onChange={(page, pageSize) => {
									setPage(page);
									setPageSize(pageSize);
								}}
							/>
						</Col>
						{customersQuery.data.data.data.map((customer) => (
							<Col key={customer._id} span={4} flex>
								<CustomerCard customer={customer} />
							</Col>
						))}
					</Row>
				</Col>
			</Row>
		)
	);
};

export default Paginate;
```