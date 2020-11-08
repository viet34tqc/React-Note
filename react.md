##Khi viết component:
	Có 2 loại component:
		Container:
			- Quản lý, xử lý dữ liệu
			- Render cái gì, không quan tâm render ui thế nào
			- Chứa các component con
		Dumb component
			- Làm nhiệm vụ render ui
			- Thường chỉ có props, không có state
			- Tái sử dụng với props khác nhau
	Cần xác định:
		- props
		- state: có giá trị nào thay đổi ảnh hưởng tới render không
		- render cái gì

##forEach vs map khi loop trong react
	foreach không return gì
	map return array nên sẽ in ra giá trị

##Khi chèn style cho 1 component thì phải bỏ ra 1 thẻ div, nếu không react sẽ hiểu đấy là 1 prop

	<div style={showWhenVisible}>
	<LoginForm
		handleSubmit={handleLogin}
	/>
	</div>

##export và export default:
	1 module chỉ có 1 default export nhưng có thể có nhiều normal export
	export const createNote = (content) => {
	  // ...
	}

	export const toggleImportanceOf = (id) => {
	  // ...
	}
	export default noteReducer

	Khi import:
	import {
	  createNote, toggleImportanceOf
	} from './reducers/noteReducer';
	import noteReducer from "./reducers/noteReducer";

##React Ref:
	1 cách khác ngoài prop để tương tác với child component
	Hạn chế sử dụng vì nó phá bỏ quy tắc encapsulation của component
	Thường dùng để triggering focus DOM note trong child component

	B1: Tạo ref
		const noteRef = React.createRef();
	B2: Truyền nó vào child component
		<Togglable buttonLabel="new note" ref={noteFormRef}>
	B3: Trong child component để gọi ref ra thì phải gọi thông qua React.fowardRef( props, ref ) => {})
		Ở parent để access ref thì access thông qua noteRef.current

	Truyền 1 hàm từ child sang parent
		Dùng useImperativeHandle
		useImperativeHandle(ref, () => {
			return {
				toggleVisibility // toggleVisibility: toggleVisibility
			};
		});

		Ở parent:
			noteFormRef.current.toggleVisibility();

	Ref chỉ được gán bằng 1 thứ, ví dụ như ở trên là gán bằng 1 object có chứa 1 method
	Nếu gán <button ref={ref}></button> thì ref ở button sẽ bị loại bỏ

##Hook:
	useReducer:
		gần giống useState
		update State dựa trên current state theo cách tương tự như hàm reduce ( cũng có 1 hàm callback và 1 giá trị initial)
		khác useState là ngoài initialState, useReducer truyền luôn callback vào. Hàm này sẽ chạy khi thay đổi state.
		HÀM CALLBACK NHẬN 2 GIÁ TRỊ: current state và 1 tham số khác mình truyền vào hàm setState


	function cartReducer(state, product) {
		return [...state, product]
	}
	...
	const [cart, setCart (thường chỗ này sẽ viết là hàm dispatch)] = useReducer(cartReducer, []); //[] là giá trị ban đầu của cart
	function add(product) {
		setCart(product.name);
	}
	<button onClick={() => add(product)}>Add</button>

	Khi setCart gọi thì sẽ gọi luôn hàm cartReducer

##React Context:
	Tại sao phải sử dụng:
		Giúp truyền state xuống các component mà không phải truyền qua props
	Khai báo:
		Trong file MovieContext.js, export 2 cái
			+ export const MovieContext = createContext();
			+ export component Provider, component này có 1 props là các component nhận state.
			export const MovieProvider = ( props ) => {
				const [ movies, setMovies ] = useState( [
					{
						name: 'Harry Potter',
						price: '$10',
						id: 12345,
					},
					....
				] );

				return (
					<MovieContext.Provider value={ [ movies, setMovies ] }>
						{props.children }
					</MovieContext.Provider>
				);
			};
	Cách sử dụng:
		Khai báo các component nhận state từ provider:
			<MovieProvider>
				<Nav />
				<AddMovie />
				<MovieList />
			</MovieProvider>
		Trong mỗi component, để lấy state ra thì thay useState = useContext
			const [ movies, setMovies ] = useContext( MovieContext );

##Redux:
	Tại sao phải sử dụng redux:
		Giúp quản lý state. State của toàn bộ ứng dụng được lưu trong 1 store duy nhất.
		Có thể chuyển phần xử lý state của các component con vào chính component đó (trước là phải khai báo ở component cha rồi truyền xuống)
	Các khái niệm cần quan tâm:
		Action: 1 object, để thay đổi state, có thể truyền data vào
			{
				type: 'INCREMENT',
				data: { id }
		 	}
		Reducer: 1 hàm
			Tham số đầu vào là state và action
			Ở đây sẽ khai báo những thay đổi của state tương ứng với action truyền vào
			Không được gọi trực tiếp mà phải truyền vào createStore
			const reducer = ( state = 0, action) => {}
		createStore: tạo store
			import {createStore} from 'redux'
			Tham số truyền vào là reducer
			const store = creatStore( reducer );
		Dispatch: thực thi 1 action, ví dụ như bấm nút thì dispatch action tương ứng
      		<button onClick={e => store.dispatch({ type: "INCREMENT" })}>plus</button>
		subscribe: 1 hàm
			Chạy 1 hàm callback khi có bất kì sự thay đổi state nào
			store.subscribe(() => {
				const storeNow = store.getState()
				console.log(storeNow)
			})

	Flow:
	Action creators create objects → objects are dispatched to the store →
	the store invokes reducers → reducers generate new state → listeners are notified of state updates.

	React-redux:
		npm install --save react-redux
			Dùng component <Provider> để kết nối component với store

			import { Provider } from "react-redux";

			ReactDOM.render(
			<Provider store={store}>
				<App />
			</Provider>,
			document.getElementById("root")
			);

			Nếu các component con cần dùng store thì App phải truyền prop store xuống

		Sử dụng các hook của react-redux
			import { useSelector, useDispatch } from 'react-redux'

			thay store.dispatch bằng useDispatch;
				const dispatch = useDispatch()
				dispatch(createNote(content))

			thay thế store.getState() = useSelector:
				const notes = useSelector(state => state)
				Ở đây có thể filter trả về data cần thiết
					const importantNotes = useSelector(state => state.filter(note => note.important))

		Chuyển tất cả reducers vào 1 thư mục reducers:
			VD: noteReducer.js
				const noteReducer = (state = initialState, action) => {
					switch (action.type) {
						case "NEW_NOTE":
							return state.concat(action.data);
						case "TOGGLE_IMPORTANCE":
							...
							return state.map((note) => (note.id !== id ? note : changedNote));
						default:
							return state;
					}
				};

				Tạo action creator:
				export const createNote = (content) => {
					return {
						type: "NEW_NOTE",
						data: {
							content,
							importance: false,
							id: generateId(),
						},
					};
				};
		Redux thunk:
			1 middleware cho phép viết action trả về function thay vì 1 object bằng cách trì hoãn việc đưa action đến reducer
			Redux thunk sẽ kiểm tra giá trị truyền vào hàm dispatch
				Nếu là object thì sẽ chuyển action đấy đến reducer
				Nếu là 1 function thì redux thunk sẽ gọi hàm đó, nó sẽ đợi hàm đó chạy xong rồi mới truyền đến reducer
			Sử dụng dispatch ngay trong khai báo action creator

			Không có redux thunk:
			Action Creator -> Action -> dispatch -> Reducers -> State
			Có redux thunk:
			Action Creator -> Action -> dispatch -> middleware -> Reducers -> State

##React Router:
	cài đặt: npm install --save react-router-dom

	1 số components
	import {
	BrowserRouter as Router,
	Switch, Route, Link
	} from "react-router-dom"

	Router: component này chứa tất cả link và component hiển thị tương ứng
		<Router>
			<>
				<Link to="/">home</Link>
				<Link to="/notes">notes</Link>
			</>

			<Switch>
			</Switch>
		</Router>

		Link: link trên navigation, click sẽ thay đổi URL
			<Link to="/">home</Link>

		Switch: đưa các components cần hiển thị vào trong component này:
		<Switch>
			<Route path="/notes">
				<Notes />
			</Route>

			<Route path="/">
				<Home />
			</Route>
		</Switch>

		Route: hiển thị component tương ứng với URL
			<Route path="/notes">
				<Notes />
			</Route>
			path sẽ tương ứng với <Link> khai báo ở trên

		Switch sẽ render component đầu tiên mà có patch match với URL
		Thứ tự các component trong switch cũng rất quan trọng, nếu đặt path="/" thì sẽ không có gì hiển thị

	useParam(): dùng để lấy parameter trên url
		VD: URL: http://localhost:3000/notes/3 thì useParam() trả về {id: '3'}
		parameter id được khai báo khi khai báo Route

		<Route path="/notes/:id">
			<Note notes={notes} />
		</Route>

	useHistory(): dùng để thay đổi url:
		useHistory().push( '/' );

##async, await và promise:
	await phải dùng ở trong async
	await sẽ trả về data chứ không trả về promise
	còn cả async thì sẽ trả về promise
		Không dùng async, await:
		const getAll = () => {
			const request = axios.get(baseUrl);
			return request.then(response => response.data); // trả về 1 promises. Nếu promise này dùng then thì response.data sẽ là tham số truyền vào
		};

		Dùng async, await:
		const getAll = async () => {
			const response = await axios.get(baseUrl); // await trả về data, không trả về promise.
			return response.data
		};

##Middleware: phần kết nối giữa request và response
	+ Xử lý, lọc request. Ví dụ Express sẽ có CORS Middleware để kiểm tra cross-origin Resource
	CRSF Middleware để xác thực CSRF của request
	+ Điều chỉnh response

##Kết nối backend và frontend:
	- Ở backend install package cors middleware
	- Khi ở frontend đổi tên base url thành api/persons thì ở backend phải thêm vào package.json
	"proxy": "http://localhost:3001"
	React app sẽ redirect từ 3000 -> 3001
	- frontend npm run build
	- copy thư mục build vào thư mục backend
	- ở backend add thêm middleware: app.use(express.static('build'))

##nodejs:
res là response trả về khi có request
	res.status(404).end();
	res.json( note.toJSON() );
	res.send( 'dsadas' ) // in ra màn hình

Tìm theo id:
	Note.findById( req.params.id ).then( note => {
		res.json(note.toJSON() )
	}).catch( error => next( error) )

Tìm theo id và delete:
	Note.findByIdAndDelete( req.params.id );

Deploy lên heroku
	- Cài heroku: npm install -g heroku
	- Tạo app: heroku create
		Nếu muốn thêm name cho app thì sẽ là heroku create app_name
		Trong trường hợp 1 repo có nhiều app:
			Mặc định là heroku sẽ tạo 1 remote tên là heroku tương ứng khi tạo app.
			Nếu muốn đổi tên thì phải thêm --remote app-1
				heroku create app-1-name(ten_app) --remote app-1(ten-remote)
				heroku create app-2-name(ten_app) --remote app-2(ten-remote)
	- Ở backend:
		+ Tạo Procfile: web: node index.js để chỉ cho Heroku biết làm cách nào để start application
		+ Sửa lại port ở file index.js:
			const PORT = process.env.PORT || 3001
			app.listen(PORT, () => {
			  console.log(`Server running on port ${PORT}`)
			})
		+ Push lên heroku:
			+ Trong trường hợp chỉ push lên 1 app duy nhất trên heroku: git push heroku master ( tùy theo branch hiện tại: có thể git push heroku ten_branch:master )
			+ Trong trường hợp 1 repo có nhiều app:
				- Đã có remote được tạo như ở trên
					- app1:
						- git push app-1 master
						hoặc git push app-1 ten_nhanh:master
					- app2:
						- git push app-2 master
						hoặc git push app-2 ten_nhanh:master
				- Chưa có remote và bây h mới tạo remote:
					git remote add app-1 https://git.heroku.com/app-1.git
					git remote add app-2 https://git.heroku.com/app-2.git
					Với https://git.heroku.com/app-1.git là đường dẫn git (trong Dashboard->Settings)
					Sau đó thì mới push
		+ Nếu có thay đổi thì git add rồi push lại lên heroku
		+ Nếu có nhiều app thì khi sử dụng command bắt đầu bằng heroku thì sẽ bị lỗi
			Multiple apps in git remotes
			=> phải thêm --remote remote_name hoặc --app app_name
		+ Để xem log cho từng app thì khi dùng lệnh heroku logs -t phải thêm
			heroku logs -a app_name -t

##MongoDB và Express
	+ Tạo 1 cluster trên mongodb
	+ Tiếp theo vào database access tạo user để kết nối tới CÁC database của cluster này
	+ 1 database sẽ có nhiều collections, mỗi collection thì có nhiều document
	+ Collection thì tương tự như table
	+ Document thì tương tự như record

	Tách riêng phần model ra ngoài:
	+ Cài npm i dotenv --save
	+ Tạo file .evn, thêm file này vào gitignore
	+ Thêm URI và port kết nối tới MongoDB vào đây:
		MONGODB_URI=mongodb+srv://fullstack:sekred@cluster0-ostce.mongodb.net/note-app?retryWrites=true
		PORT=3001
	Cách kết nối mongoDB:
		Require file .env để lấy thông số
		require("dotenv").config();

		Require mongoose:
		const mongoose = require("mongoose");

		Lấy url từ file env
		const url = process.env.MONGODB_URI;

		Connect to mongoose
		mongoose.connect(url, { useNewUrlParser: true })

		Tạo schema
		const phonebookSchema = new mongoose.Schema({
			name: String,
			phone: String
		});

		Tạo và export model:
		module.exports = mongoose.model("Phonebook", phonebookSchema);

	+ Kết nối giữa các Database:
		const userSchema = new mongoose.Schema({
			....
			notes: [
				{
					type: mongoose.Schema.Types.ObjectId,
					ref: 'Note',
				},
			],
		});

		const noteSchema = new mongoose.Schema({
			...
			user: {
				type: mongoose.Schema.Types.ObjectId,
				ref: 'Users',
			},
		});

		ref: tên Modal

	+ File nào lấy giá trị từ file .env
		require("dotenv").config();
	+ Ở file khác lấy model như sau:
	const Person = require('./models/Person');


	Express:
	Basic routing:
	const express = require( 'express' );
	// Express app
	const app = express();
	const port = 3000

	app.get('/', (req, res) => res.send('Hello World!'))

	app.listen(port, () => console.log(`Example app listening on port ${port}!`))

	GET theo ID:
		Note.findById(req.params.id).then(note => {
			response.json(note.toJSON());
		});

	POST:
		+ tạo note mới (document) trên mongodb:
			const note = new Note({
				content: body.content,
				important: body.important || false,
				date: new Date()
			});
		+ save note:
			note.save().then(savedNote => {
				res.json(savedNote.toJSON());
			});


	Error Handler:
		Dùng hàm next();
			app.get("/api/notes/:id", (req, res, next) => {
				Note.findById(req.params.id)
				.then(note => {
					if (note) {
						res.json(note.toJSON());
					} else {
						res.status(404).end();
					}
				})
				.catch(error => next( error ));
			});
		Nếu next() được truyền vào tham số thì luồng sẽ chạy tiếp tới error handler middleware bên dưới
			const errorHandler = (error, req, res, next) => {
				console.log(error.message);

				if (error.name === "CastError" && error.kind === "ObjectId") {
					return res.status(400).send({ error: "malformated id" });
				}

				next(error);
			};

		Sau đó, load middleware:
			app.use(errorHandler);

		Thứ tự load middle ware giống thứ tự của code

		Có 2 kiểu trả về (gồm cả data và dữ liệu)
		Nếu user gửi request tới endpoint thì kết quả trả về luôn ở dạng json, kể cả lỗi hay data
		=> dùng hàm json() hoặc send() và return nó. Ví dụ
		if (error) {
			return res.status(400).json({
				error
			});
		}
		Dùng return hay không return ở trên thì đều chạy. Sử dụng return
		ở trên là để trả lỗi về sớm và không xử lý đoạn đằng sau

		Còn nếu không cần trả về người dùng data gì thì dùng end(). Ví dụ
		res.status(404).end();

	Validation: của Mongoose
		Lúc khai báo schema thì thêm vào
			new mongoose.Schema({
				content: {
					type: String,
					minlength: 5,
					required: true
				},
				date: {
					type: Date,
					required: true
				},
				important: Boolean
			});

##Test backend:
	Dùng package supertest

	const app = require('../app');
	const api = supertest(app);

	Refressh lại database trước mỗi test case:
		// Before every test.
		beforeEach(async () => {
		await Note.deleteMany({});

		const noteObjects = helper.initialNotes.map(note => new Note(note));
		const promiseArray = noteObjects.map(note => note.save());
		await Promise.all(promiseArray);
		});

	describe('when there is initially some notes saved', () => {
		test('notes are returned as json', async () => {
			await api
			.get('/api/notes')
			.expect(200)
			.expect('Content-Type', /application\/json/);
		});
	});

##GraphQL:
	Khác biệt với REST:
		định nghĩa được query gửi đi
		định nghĩa được dữ liệu trả về.
	Các khái niệm cần quan tâm:
		Schema: định nghĩa kiểu dữ liệu
			type Person {
				name: String!,
				phone: String!
			}
			type Query {
				personCount: Int! // Dữ liệu trả về không được rỗng và ở dạng Integer.
				allPersons: [Person!]! // Trả về 1 array gồm các Person object.
				findPerson(name: String!): Person // phải có tham số name dạng string, có thể trả về null hoặc Person object
			}

			Kiểu dữ liệu là string
			Dấu ! tức là dữ liệu không được rỗng
			Mỗi 1 schema sẽ có 1 kiểu Query, mô tả loại query

	Cấu trúc 1 query để lấy dữ liệu
		query {
			tên_query {
				tên_field
			}
		}

	Apollo server:
		const server = new ApolloServer({
			typeDefs,
			resolvers,
		})

		typeDefs: GraphQL Schema
		resolver: khai báo cách dữ liệu trả về

		const typeDefs = gql`
			type Person {
				name: String!
				phone: String
				street: String!
				city: String!
				id: ID!
			}

			type Query {
				personCount: Int!
				allPersons: [Person!]!
				findPerson(name: String!): Person
			}`

		const resolvers = {
			Query: {
				personCount: () => persons.length,
				allPersons: () => persons,
				findPerson: (root, args) =>
				persons.find(p => p.name === args.name)
			}
		}
