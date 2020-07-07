org.hibernate.hql.internal.ast.QuerySyntaxException: Unable to locate appropriate constructor、





Do not request a bean from a BeanFactory in a destroy method implementation!





Caused by: org.springframework.beans.factory.BeanCreationNotAllowedException: Error creating bean with name 'threadPoolTaskExecutor': Singleton bean creation not allowed while singletons of this factory are in destruction (Do not request a bean from a BeanFactory in a destroy method implementation!)

bean正在被销毁，就又去创建

//this.singletonsCurrentlyInDestruction是个boolean，记录的是当前这个单例是否正在被销毁，
				//如果是true，代表单例已经执行了自身的destroy销毁方法，或者有异常的时候执行了destroySingleton方法等情况
				if (this.singletonsCurrentlyInDestruction) {
					throw new BeanCreationNotAllowedException(beanName,
							"Singleton bean creation not allowed while singletons of this factory are in destruction " +
							"(Do not request a bean from a BeanFactory in a destroy method implementation!)");
				}
————————————————
