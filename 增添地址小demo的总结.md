**整个小功能做的步骤：**

1. 关于界面的编写，布局就不多说了，很简单，主要有的要注意的就是LinearLayout的布局默认是水平排列的，需要加入oreatation的属性，改为vertical；

2.  还有就是权重的使用，如果是水平方向上，两个都可以加上weight（两个所占的比例），也可以只给一个设置权重（越小优先级越高），另一个是wrap，目的是它的优先级的比较高，它优先分配，自包裹的那个占得小，

3.  Listview 的属性中，它的宽和高一定要设置成match；
 
4.  设置BaseAdapter适配器，需要有一个有参构造，传过来几个参数，

        public MyAdapter(Context context, List<Address> list) {
		this.context = context;
		this.list = list;
	    }
5.   在listview那个activity中需要找控件，设置数据，引用适配器，当然也可以设置点击监听，长按监听，

        // 查找list控件
		lv_address = (ListView) findViewById(R.id.lv_address);
		// 初始化数据
		initData();
        adapter = new MyAdapter(this, list);
		lv_address.setAdapter(adapter);
        //设置长按监听
        setOnItemLongClickListener(new OnItemLongClickListener() {}

6.  适配器的代码优化，listview的工作原理，复用convertView，找控件也不必找多次，
  
         ViewHolder vh = null;
		if (convertView == null) {
			vh = new ViewHolder();
			view = View.inflate(context, R.layout.item_list, null);
			vh.tv_name = (TextView) view.findViewById(R.id.tv_name);
			vh.tv_phone = (TextView) view.findViewById(R.id.tv_phone);
			vh.tv_address = (TextView) view.findViewById(R.id.tv_address);
			view.setTag(vh);
		} else {
			view = convertView;
			vh = (ViewHolder) view.getTag();
		}
		vh.tv_name.setText("收件人："+list.get(position).getName());
		vh.tv_phone.setText("电话："+list.get(position).getPhone());
		vh.tv_address.setText("收件人地址："+list.get(position).getAddress());
		return view;

7.  设置快捷菜单，长按条目后会出现菜单，首先在menu文件夹下建一个.xml文件，在onCreateContextMenu（）中引用建立的那个布局，记住，还要为快捷菜单注册一下才行

        registerForContextMenu(lv_address);
        getMenuInflater().inflate(R.menu.main, menu);
 
也可以使用下面的方法来添加菜单的名字

        menu.add(Menu.NONE, 1, Menu.NONE, "更新");
		menu.add(Menu.NONE, 2, Menu.NONE, "删除");

然后重写回调方法onContextItemSelected方法，这里有个api，可以获得所选条目的位置，也就是索引号

    @Override
	public boolean onContextItemSelected(MenuItem item) {

		//获取选中条目的文本内容
		
		//1.获取菜单的额外信息，包含所选条目的position
		AdapterContextMenuInfo menuInfo = (AdapterContextMenuInfo) item.getMenuInfo();
		
		//2.获取position
		final int position = menuInfo.position;
		
		//3.根据position获取条目对象
		
		blackInfo = list.get(position);

8.更新和删除，list集合更新后需要通知adapter进行更新
		
		switch (item.getItemId()) {
		case 1://更新
			
			
			AlertDialog.Builder builder= new Builder(this);
			builder.setTitle("更新"+blackInfo.getNum());
        //可以new一个EditText，因为对话框只需要一个EditText
			
			final EditText et = new EditText(this);
			
			et.setHint("请输入新号码：");
			builder.setView(et );
			
			
			builder.setPositiveButton("确定", new OnClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which) {
					
					//获取et输入的新号码内容
					
					String num = et.getText().toString();
					
					
					//调用dao的修改方法
					BlackInfo newBlack = new BlackInfo(num);
					dao.update(blackInfo.getId(), newBlack);
					//listview更新
					list.set(position, newBlack);
					adapter.notifyDataSetChanged();									
				}
			});
			
			builder.setNegativeButton("取消", null);

			builder.show();
			break;
		case 2://删除
			
			//调用dao的delete方法
			
			boolean delete = dao.delete(blackInfo.getId());
			//listview更新
			if (delete) {
				list.remove(position);
				adapter.notifyDataSetChanged();
			}

			break;
		}
		return super.onContextItemSelected(item);

9.与数据库建立连接，首先得建一个类继承SQLiteHelper，重写两个方法，再来一个有参构造，接收传过来的参数。大多数的时候，这些参数是
Context context 对象，有时候还会有 List<object> list 这样的。
   
10.其实上面的9并没有创建数据库，必须得先创建9那个类的对象，然后调用它的getReadabledatabase 或者 getWriteableDatabase的方法，才真正建立，通常来说，在公司做项目时，数据库不用关闭，因为经常要用，
数据库的CRUD(增删改查）最好放在一个DAO类中。

    public class DAO{
      private DBHelper helper;

	  public BlackDAO(Context context) {
	  helper = new DBHelper(context);
	  }

	/**
	 * 查询，
	 * @return返回list集合
	 */
	public List<BlackInfo> query() {

		// 获取数据库连接对象
		SQLiteDatabase db = helper.getReadableDatabase();

		Cursor cursor = db.query(DBHelper.TABLE_NAME, null, null, null, null,
				null, null);

		List<BlackInfo> list = new ArrayList<BlackInfo>();
		while (cursor.moveToNext()) {

			int id = cursor.getInt(0);
			String num = cursor.getString(1);
			list.add(new BlackInfo(id, num));

		}
		cursor.close();
		db.close();

		return list;

	}

	/**
	 * 添加
	 * @param blackinfo 要添加的黑名单号码对象
	 * @return添加是否成功
	 */
	public boolean add(BlackInfo blackinfo) {
		
	SQLiteDatabase db = helper.getReadableDatabase();
		
		ContentValues values=new ContentValues();
		
		values.put("num", blackinfo.getNum());
		long insert = db.insert(DBHelper.TABLE_NAME, null, values);
		db.close();
		return insert>0;

	}

	/**
	 * 修改
	 * @param id 要修改的编号
	 * @param blackinfo 新黑名单对象
	 * @return
	 */
	public boolean update(int id, BlackInfo blackinfo) {
		SQLiteDatabase db = helper.getReadableDatabase();
		
		ContentValues values=new ContentValues();
		
		values.put("num", blackinfo.getNum());
		
		int update = db.update(DBHelper.TABLE_NAME, values, "_id=?", new String[]{id+""});
		
		
		db.close();
		return update>0;
	}

	/**
	 * 
	 * 删除
	 * @param id 编号
	 * @return
	 */
	public boolean delete(int id) {
		//1.
		SQLiteDatabase db = helper.getReadableDatabase();
		
		//2.
		int delete = db.delete(DBHelper.TABLE_NAME, "_id=?", new String[]{id+""});
		
		//3.
		db.close();
		
		return delete>0;
	   }
    }
